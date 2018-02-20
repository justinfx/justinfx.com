---
date: 2016-05-14 15:53:48 +1200
title: C++ Bindings For A Go Library
type: post
slug: cpp-bindings-for-go
categories:
- Code
tags:
- c++
- go
- golang
- bindings
- cgo
author: ''
excerpt: ''
url: ''
meta_keywords: []
thumbnail: []
meta_description: []
suf_meta_keywords: []
suf_thumbnail: []
suf_meta_description: []
featured_image: []
suf_featured_image: []
enclosure: []

---
This is an overview describing my approach to creating C++ bindings around a Go library.

<!--more-->

## A bit about wrapping C/C++

I've had some past experience in writing cgo bindings on top of C and C++ libraries.

**Author**

* [OpenImageigo](https://github.com/justinfx/openimageigo) and [OpenColorigo](https://github.com/justinfx/opencolorigo) - Color pipeline and image manipulation and conversion libraries relevant to the Visual Effects industry. These cgo libraries wrap C++.

**Contributor**

* [imagick](https://github.com/gographics/imagick) - Bindings on top of the C-API for ImageMagick; Image manipulation
* [pigosat](https://github.com/wkschwartz/pigosat) - Bindings on top of Picosat C-API; Satisfiability solver.

C libraries are more straight-forward to wrap, because Go can access them directly. C++, on the other hand, requires a "shim" layer written in C. This layer has to handle the calls between Go and the target C++ library. An example would be to treat a C++ class as an opaque pointer and provide C functions that accept the pointer and delegate to the C++ methods. Or to convert between vectors and arrays:

_ref:_ [_github.com/justinfx/openimageigo/cpp/oiio.h_](https://github.com/justinfx/openimageigo/blob/master/cpp/oiio.h)

{{< highlight cpp >}}
// oiio.h
\#ifdef __cplusplus
extern "C" {
\#endif

typedef void ImageInput;

// imageinput.cpp
ImageInput\* ImageInput_Open(const char\* filename, const ImageSpec _config) {
std::string s_filename(filename);
return (ImageInput_) OIIO::ImageInput::open(
s_filename,
static_cast<const OIIO::ImageSpec\*>(config));
}

const char\* ImageInput_format_name(ImageInput \*in) {
return static_cast[OIIO::ImageInput\*](OIIO::ImageInput\*)(in)->format_name();
}
{{< /highlight >}}

As you can see, we make an opaque `ImageInput\*` available to Go, and create a shim layer that is C compatible. `ImageInput_Open()` is a factory function that wraps the static C++ `ImageInput::open()` equivalent. We just cast between our pointer and theirs (OIIO's), in order to call functions and methods.

But like I said, these are Go bindings on top of C/C++ libraries. What about exposing a C++ library on top of a pure Go library?

## Exposing Go libraries to C++

I had previously written a Go library called [gofileseq](https://github.com/justinfx/gofileseq). This library also caters to the Visual Effects industry, providing a way to build, parse, and find sequences of files, and frame ranges. Such as dealing with:

    /path/to/some/file_foo.0100.exr
    /path/to/some/file_foo.1-100#.jpg
    /path/to/some/file_foo.1-50x2,100-200x3@@@.tif

`gofileseq` library is a port of a python equivalent, [fileseq](https://github.com/sqlboy/fileseq), which I help to maintain. If the parsing rules need to be updated in the Python library, they should also be updated to match in the Go library, to maintain compatibility.

I had been asked more than once by colleagues if a C++ version of fileseq was available. It wasn't, and I also didn't want to have to maintain a 3rd standalone port that should follow the same behavior as the other two. But since the introduction of the [Go ](https://golang.org/doc/go1.5#link)`[-buildmode](https://golang.org/doc/go1.5#link)` flag in 1.5, it has become possible to export functions from Go to C/C++

{{< highlight go >}}
// sum.go
package main

import "C"

//export sum
func sum(x, y int) int {
return x + y
}

func main() {

}
{{< /highlight >}}

```shell
$ go build -buildmode=c-shared -o sum.so
$ ls
sum.c  sum.go sum.h  sum.so
```

Now we have a shared library and a header file, to use in our C app

{{< highlight c >}}
// sum.c
\#include <stdio.h>
\#include "sum.h"

int main(void) {

    int z = sum(1, 2);
    printf("1+2=%d\n", z);
    
    return 0;

}
{{< /highlight >}}

```shell
$ gcc sum.c sum.so -o sum && ./sum
1+2=3
```

This looks pretty straight-forward, right? It would seem so until you encounter the need to export instances of your types from Go to C, and these types contain pointers to other types. You see, there are certain rules about what you can do when communicating via cgo. And these rules were [made official as off Go 1.6](https://golang.org/cmd/cgo/#hdr-Passing_pointers).

Some of those rules are:

1. You can pass Go pointers to C, but C should not hang on to them beyond the scope of the call.
2. You can not pass memory to C that contains pointers. That is, a struct which has pointer fields.

These pose an issue if you want to be able to have your C++ library create instances of objects in Go and hang on to them longer than the Go function call. In the case of `gofileseq`, the main two objects are `[FileSequence](https://godoc.org/github.com/justinfx/gofileseq#FileSequence)` and `[FrameSet](https://godoc.org/github.com/justinfx/gofileseq#FrameSet)`. These are constructed from strings, perform parsing, and maintain private state. So a user would want to construct them and keep them around until they are done calling methods on them. But according to the cgo rules, I can't give a pointer to C++ for it to hang on to indefinitely, and I can't create a C struct in a shim to populate, because there are more internal pointers involved in a `FileSequence`. Basically I saw this as a more complex type than just a struct with simple data fields.

How do I construct these instances for C++? I created package-private maps using generated `uint64` keys, and the instances as values. Actually, I wrap the instances up in a struct that also tracks reference counts. This allows C++ to manage the lifetime of the objects.

The full implementation is located here: [github.com/justinfx/gofileseq/cpp](https://github.com/justinfx/gofileseq/tree/master/cpp)

{{< highlight go >}}
type FileSeqId uint64

type fileSeqRef struct {
\*fileseq.FileSequence
refs uint32
}

type fileSeqMap struct {
lock \*sync.RWMutex
m    map\[FileSeqId\]\*fileSeqRef
rand idMaker
}

type idMaker interface {
Uint64() uint64
}

func (m \*frameSetMap) Incref(id FrameSetId) {
// Inc refs
}

func (m \*frameSetMap) Decref(id FrameSetId) {
// Dec refs
// If refs == 0, delete from map
}
{{< /highlight >}}

Now when C++ wants to create a `FileSequence`, they can call a `New` function which will create an instance, add it to the map, and return a uint64 id handle

{{< highlight go >}}
//export FileSequence_New
func FileSequence_New(frange \*C.char) (FileSeqId, Error) {
fs, e := fileseq.NewFileSequence(C.GoString(frange))
if e != nil {
// err string is freed by caller
return 0, C.CString(e.Error())
}

    id := sFileSeqs.Add(fs)
    return id, nil

}

//export FileSequence_Dirname
func FileSequence_Dirname(id FileSeqId) \*C.char {
fs, ok := sFileSeqs.Get(id)
// caller must free string
if !ok {
return C.CString("")
}
return C.CString(fs.Dirname())
}
{{< /highlight >}}

You can see here that because this is C++ calling Go, we aren't able to free the C string from Go, as the string needs to outlive the Go call. We just make sure that C++ always frees `\*C.char` when it is returned. To make sure I always clean up the `char\*` in C++, I made a helper `StringProxy` class which wraps it and cleans it up in the destructor:

_ref:_ [_github.com/justinfx/gofileseq/cpp/private/fileseq_p.h_](https://github.com/justinfx/gofileseq/blob/master/cpp/private/fileseq_p.h#L16)

{{< highlight cpp >}}
class StringProxy {
public:
StringProxy(char\* managed) : m_data(managed), m_str() {
if (managed != NULL) {
m_str.assign(managed);
}
}

    ~StringProxy() {
        if (m_data != NULL) {
            free(m_data);
            m_data = NULL;
        }
    }
    //...

};
{{< /highlight >}}

This all ended up working fine! I created some benchmarks to compare fileseq between Python, C++, and Go. The test involved looping 100k times, creating a FileSequence, and calling 4 methods on it.

{{< highlight cpp >}}
const int n = 100000;

for (int i=0; i < n; ++i) {
fileseq::FileSequence fs("/path/to/file_name.1-100x2#.ext");
str = fs.string();
str = fs.frameRange();
num = fs.start();
num = fs.end();
}
{{< /highlight >}}

    Python:  8.0s
        Go:  1.9s
       C++:  3.2s

There is a bit of overhead in the C++ lib from the fact that they are bindings on top of a Go layer, but I think the win for me is that it is still an acceptable level of performance and I don't have to maintain a 3rd standalone port of fileseq!

It bothered me a little that I did have to rely on a mutex-guarded static map, so I [asked on go-nuts about an alternative approach](https://groups.google.com/d/topic/golang-nuts/kLRFdycy0yc/discussion). Ian Lance Taylor confirmed that the guarded map approach, with opaque ids as keys, was a completely viable approach to solving this problem. But that in some circumstance it can also be possible to malloc C memory to back Go objects, in which case they are not tracked by Go's GC.

i.e., something like this:

{{< highlight cpp >}}
fs := (\*FileSequence)unsafe.Pointer(
C.malloc(unsafe.Sizeof(FileSequence{})))
{{< /highlight >}}

I tried to apply this approach, but it became really tedious and confusing to figure out how to malloc all of the internal data nested in the types. For instance, `FrameSet` has methods that create more data along the way. And I had no idea how to cleanly get it to keep malloc'ing memory so the entire structure continues to be 100% managed by C++. So I gave up on trying this route, although I bet it would work for less nested structs.

If anyone has any suggestions on another way to improve the process of exporting instances of Go objects to C++, I would definitely welcome feedback! And for anyone wanting to make their Go libraries available to C++, I hope this overview has been useful!