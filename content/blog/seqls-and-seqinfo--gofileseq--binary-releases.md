+++
author = ""
categories = []
date = "2018-02-20T13:48:07+00:00"
draft = true
enclosure = []
excerpt = "gofileseq is a Go language library for parsing file sequence strings commonly used in VFX and animation applications."
featured_image = []
meta_description = ["Binary releases for seqls & seqinfo tools (gofileseq)"]
meta_keywords = []
slug = "gofileseq-binary"
suf_featured_image = []
suf_meta_description = []
suf_meta_keywords = []
suf_thumbnail = []
tags = []
thumbnail = []
title = "seqls & seqinfo (gofileseq) binary releases"
type = "post"
url = ""

+++
<img src="https://assets-cdn.github.com/images/modules/logos_page/GitHub-Mark.png" style="width:100px; float:left;">

[gofileseq](https://github.com/justinfx/gofileseq "gofileseq") is a Go language library for parsing file sequence strings commonly used in VFX and animation applications. 

<!--more-->

<p style="clear:both">

#### Sequences of files follow a pattern similar to

    /path/to/some/file_foo.0100.exr
    /path/to/some/file_foo.1-100#.jpg
    /path/to/some/file_foo.1-100@@@.tif

#### Support range formats

           Standard: 1-10
        Comma Delim: 1-10,10-20
            Chunked: 1-100x5
             Filled: 1-100y5
          Staggered: 1-100:3 (1-100x3, 1-100x2, 1-100)
    Negative frames: -10-100
            Padding: #=4 padded, @=single pad

#### Tools

Included with the library are two command line utilities:

[seqls](https://github.com/justinfx/gofileseq/tree/master/cmd/seqls) - An `ls`-style tool for searching and listing file sequences

    $ seqls -r 
    
    file_foo.0100.exr
    subdir/file_foo.1-100#.jpg
    subdir2/file_foo.1-100@@@.tif

[seqinfo](https://github.com/justinfx/gofileseq/tree/master/cmd/seqinfo) - Parses one or more sequence pattern strings and list plain text or json info

    $ seqinfo --json "/path/to/file.exr" "/path/to/seq.-10-200x2@@.exr"
    {
        "/path/to/file.exr": {
            "error": "",
            "string": "/path/to/file.exr",
            "dir": "/path/to/",
            "base": "file",
            "range": "",
            "pad": "",
            "ext": ".exr",
            "start": 0,
            "end": 0,
            "length": 1,
            "zfill": 0,
            "hasRange": false
        },
        "/path/to/seq.-10-200x2@@.exr": {
            "error": "",
            "string": "/path/to/seq.-10-200x2@@.exr",
            "dir": "/path/to/",
            "base": "seq.",
            "range": "-10-200x2",
            "pad": "@@",
            "ext": ".exr",
            "start": -10,
            "end": 200,
            "length": 106,
            "zfill": 2,
            "hasRange": true
        }
    }

As of recent, the gofileseq project has adopted the use of the [goreleaser](https://goreleaser.com/) tool, which means that [tagged releases](https://github.com/justinfx/gofileseq/releases) now automatically include cross-compiled binaries of the tools for linux, osx, and windows.

[![Latest Release](https://badge.fury.io/go/github.com%2Fjustinfx%2Fgofileseq.svg)](https://github.com/justinfx/gofileseq/releases)

#### C++

There is also a [C++ port](https://github.com/justinfx/gofileseq/tree/master/cpp-port "C++ port") of gofileseq within the repository, for those C++ devs that need to work with VFX-style file sequence patterns.  Doxygen API docs are now automatically built from the master branch: [justinfx.com/gofileseq/cpp](http://justinfx.com/gofileseq/cpp)