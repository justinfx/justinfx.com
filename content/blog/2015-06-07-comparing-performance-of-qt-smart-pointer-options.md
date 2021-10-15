---
title: Comparing performance of Qt smart pointer options
author: Justin Israel
type: post
date: 2015-06-07 00:00:00 +0000
slug: comparing-performance-of-qt-smart-pointer-options
url: "/2015/06/07/comparing-performance-of-qt-smart-pointer-options/"
categories:
- Code
tags:
- benchmark
- c++
- pointers
- qt
excerpt: ''
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
There are a number of sources of information on the usage and behavioral differences of the many “smart pointer” options offered by Qt. But one thing I couldn’t find enough information on were the performance characteristics.

<!--more-->

“[Continue Using QPointer](http://www.macieira.org/blog/2012/07/continue-using-qpointer/)” describes the complicated situation that came about when the Qt project was choosing to either deprecate QPointer vs QWeakPointer APIs in Qt5, and ultimately how QPointer was preserved. It also describes how in Qt4, the QPointer has a large performance hit vs its improved backend in Qt5. So basically it says to choose performance vs compatibility.

“[What C++ Smart Pointer Implementations are available?](http://stackoverflow.com/questions/5026197/what-c-smart-pointer-implementations-are-available/5026705#5026705)“, on StackOverflow, offers a pretty nice breakdown of not only the Qt smart pointers, but also other available options via Boost and C++11.

I was in a situation where an existing code base had a number of basic classes that were stored in very large parent/child relationships, as pointers. Once I needed to start sharing pointers between some newer classes, I pretty quickly ran into issues of dangling pointers when relationships would change and objects would get deleted, and it seemed that a proper solution would be to introduce some sort of guarded/smart pointer in the mix. Each of a number of options have their own pros and cons. My first approach was just to use a QPointer at the point of creation, and only change the signature of functions that really needed to receive the QPointer. But I was concerned about performance, since more than one source referenced just how slow QPointer can be. But I didn’t want to just switch to a QWeakPointer because of the deprecation in Qt5. So I ended up doing some benchmarks and figured this information might round out the rest of the available info on the web.

The test for my benchmark involved creating a number objects, adding them to a list, and then deleting them. The options for the test include both pointers to a basic struct and a QObject, QPointers, QSharedPointers, QWeakPointers, and just for an external comparison I also included boost::shared_ptr and boost::weak_ptr. The test was performed with different numbers of objects, as well as for both Qt4 and Qt5. Here is a more detailed description of each test.

#### The Test Environment

    OSX 10.9.5 Laptop  
    2.8 Ghz Intel Core 2 Duo
    8 GB 1067 Mhz DDR3 memory
    Clang x86 64bit
    Qt 4.8.6
    Qt 5.4.2

#### Description of Tests

_test_raw_pointer_ : Creating pointers to a basic struct, adding the raw pointers to a QList, and then deleting all of the pointers

_test_raw_qobject_ : Creating pointers to QObject instances, adding them to a QList, and then deleting all of the pointers

_test_ptr_qsharedpointer_ : Creating QSharedPointers to a basic struct, and also a QWeakPointer for each, and adding the shared pointers to a QList. Then deleting them all.

_test_qobj_qsharedpointer_ : Creating QSharedPointers to a QObject, and also a QWeakPointer for each, and adding the shared pointers to a QList. Then deleting them all.

_test_qobj_qweakpointer_ : Creating QWeakPointers directly from QObject instances, adding them to a QList, and then deleting them all. This test could only be performed in Qt4, because Qt5 deprecated QObject tracking support for QWeakPointers, which can now only be used with QSharedPointer

_test_qpointer_ : Creating QPointers directly from QObject instances, adding them to a QList, and then deleting them all.

_test_boost_sharedptr_ : Creating `boost::shared_ptrs` from basic struct instances, as well as boost::weak_ptrs for each, adding the shared_ptrs to a QList, and then deleting them

_test_boost_qobj_sharedptr_ : Creating `boost::shared_ptrs` from QObject instances, as well as boost::weak_ptrs for each, adding the shared_ptrs to a QList, and then deleting them

#### The Test Results

```
Testing w/ 5000 objects        Qt4       Qt5
test_raw_pointer:              1 ms      1 ms
test_raw_qobject:              4 ms      3 ms
test_ptr_qsharedpointer:       3 ms      3 ms
test_qobj_qsharedpointer:     13 ms      6 ms
test_qobj_qweakpointer:        6 ms      -
test_qpointer:                 8 ms      7 ms
test_boost_sharedptr:          5 ms      3 ms
test_boost_qobj_sharedptr:     6 ms      4 ms

Testing w/ 50000 objects
test_raw_pointer:             12 ms     10 ms
test_raw_qobject:             41 ms     35 ms
test_ptr_qsharedpointer:      32 ms     35 ms
test_qobj_qsharedpointer:     66 ms     62 ms
test_qobj_qweakpointer:       76 ms     -
test_qpointer:               103 ms     65 ms
test_boost_sharedptr:         32 ms     31 ms
test_boost_qobj_sharedptr:    69 ms     61 ms

Testing w/ 500000 objects
test_raw_pointer:            109 ms    107 ms
test_raw_qobject:            379 ms    329 ms
test_ptr_qsharedpointer:     352 ms    354 ms
test_qobj_qsharedpointer:    709 ms    642 ms
test_qobj_qweakpointer:      811 ms    -
test_qpointer:              1109 ms    725 ms
test_boost_sharedptr:        350 ms    346 ms
test_boost_qobj_sharedptr:   680 ms    611 ms

Testing w/ 2000000 objects
test_raw_pointer:            417 ms    401 ms
test_raw_qobject:           1502 ms   1201 ms
test_ptr_qsharedpointer:    1933 ms   1383 ms
test_qobj_qsharedpointer:   3207 ms   2604 ms
test_qobj_qweakpointer:     3387 ms   -
test_qpointer:              5599 ms   2832 ms
test_boost_sharedptr:       1370 ms   1350 ms
test_boost_qobj_sharedptr:  2864 ms   2453 ms
```

{{< lightbox "/uploads/2015/06/qt4_pointers-300x157.png" "/uploads/2015/06/qt4_pointers.png" >}}

{{< lightbox "/uploads/2015/06/qt5_pointers-300x154.png" "/uploads/2015/06/qt5_pointers.png" >}}

The test shows the overhead of simply going from a simple struct to a QObject, and then the further impact of introducing various smart pointers on QObjects, and also on non-QObjects where permitted.

QPointer was the easiest change to introduce, because after you make the original class into a QObject subclass, you only need to change the code at the call site(s) where the pointers are created, and then only within the functions that specifically need to receive a guarded QPointer. For all the other function signatures, QPointer will automatically cast to an unguarded T\* and behave exactly the same as before. But you can see from the results that the performance impact can be over 10x slower than when we originally started with our non-QObject class. In Qt5, while it is still most poorly performing option, it is not too far off from the option of a QSharePointer + QWeakPointer.

The best performing option seems to be keeping the class as a non-QObject and using either QSharedPointer+QWeakPointer, or boost shared_ptr+weak_ptr. Although, this is the most intrusive change since all signatures have to be updated to accept QSharedPointer (or typedef).

In between, using a QWeakPointer with direct QObject tracking seems like a balance between the ease of slotting it in to the existing code, and performance. The classes would still need to be QObject subclasses, but only the specific functions that need a QWeakPointer would need to change. Unfortunately this is not supported anymore in Qt5 (as only the QSharedPointer support is allowed), so it becomes a choice of whether the code needs to stay clean for future compatibility, or if one just wants to make the change later when a Qt5 migration actually happens.

For a small number of object creations/deletions, the performance difference doesn’t seem like it would matter that much, but when it involves tons of objects it might be a problem. If your classes were already QObjects then there are less options to consider in the first place. And the more performant route, if you don’t care about the amount of code that has to change, would probably be to shoot for QSharedPointer + QWeakPointer.