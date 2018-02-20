+++
author = ""
categories = []
date = "2018-02-20T13:48:07+00:00"
draft = true
enclosure = []
excerpt = "gofileseq is a Go language library for parsing file sequence strings commonly used in VFX and animation applications.\nAs of recent, the gofileseq project has adopted the use of the goreleaser tool, which means that tagged releases now include cross-compiled binaries of the tools for linux, osx, and windows."
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
url = "gofileseq-binary-release"

+++
[gofileseq](https://github.com/justinfx/gofileseq "gofileseq") is a Go language library for parsing file sequence strings commonly used in VFX and animation applications. Included with the library are two command line utilities:

seqls - An ls style tool for searching and listing file sequences 

seqinfo - Parses one or more sequence pattern strings and list plain text or json info

There is also a [C++ port](https://github.com/justinfx/gofileseq/tree/master/cpp-port "C++ port") of gofileseq within the repository, for those C++ devs that need to work with VFX-style file sequence patterns.  Doxygen API docs are now automatically built from the master branch: [justinfx.com/gofileseq/cpp](http://justinfx.com/gofileseq/cpp)

As of recent, the gofileseq project has adopted the use of the [goreleaser](https://goreleaser.com/) tool, which means that [tagged releases](https://github.com/justinfx/gofileseq/releases) now include cross-compiled binaries of the tools for linux, osx, and windows. 

![](https://badge.fury.io/gh/justinfx%2Fgofileseq.svg)

Hopefully this makes it easy for users on different platforms to easily list image file sequences!