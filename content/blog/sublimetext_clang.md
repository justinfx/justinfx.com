---
date: 2016-04-17 15:22:33 +1200
title: SublimeText Editor and Clang (C++)
type: post
slug: sublimetext-editor-and-clang
categories:
- Code
tags:
- c++
- sublimetext
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
<!--more-->

My code editor of choice has been [SublimeText](https://www.sublimetext.com) for a while now. It is blazing fast for searching and doing matching, replacements, and refactoring. I also love that it is very lightweight, so that I can use it as my default shell `$EDITOR`, and also have an alias `st` which can easily open files and folders in the current or new Sublime window:

```shell
# Add directory to current sublime
$ st -a /path/to/src
# Open a new sublime window, with the current directory
$ st -n .
```

Sublime is cross-platform, which means I can have the same development environment across Linux and OSX. It also has an amazing python-based plugin framework, which is enriched by the plugin manager and plugin community, [PackageControl](https://packagecontrol.io/). While it is not a full-fledged IDE, because of the availability of so many plugin options, you can get it pretty close to being like an IDE for a number of different languages. Currently I use it for Python, Go, Markdown editing, and C++. This post outlines some simple steps for configuration a SublimeText3 project for use with C++.

Before we get into the specifics of C++ configuration, let me first give a quick overview of my SublimeText3 setup, across all of my language...

* [Anaconda](https://packagecontrol.io/packages/Anaconda) - Python language Auto-complete, Goto Definition, and Documentation
* [Cython+](https://packagecontrol.io/packages/Cython%2B) - Cython language Syntax support
* [GoOracle](https://packagecontrol.io/packages/GoOracle) - Go language support for using "Oracle" tool
* [GoSublime](https://packagecontrol.io/packages/GoSublime) - IDE-like support for Go language
* [Markdown Preview](https://packagecontrol.io/packages/Markdown%20Preview) - Markdown highlighting and previewing
* [MayaSublime](https://packagecontrol.io/packages/MayaSublime) - Written by myself; Send Python/MEL code into Maya
* [PrettyJSON](https://packagecontrol.io/packages/Pretty%20JSON) - Reformatting and validating JSON
* [SideBarEnhancements](https://packagecontrol.io/packages/SideBarEnhancements) - Amp up the side bar and context menu
* [ThriftSyntax](https://packagecontrol.io/packages/ThriftSyntax) - Syntax highlighting for [Apache Thrift](https://thrift.apache.org/) spec files

And the ones we will be looking at today, for C++ support:

* [Clang-Complete](#clang-complete)
* [Switch File Deluxe](#switch-file-deluxe)
* [Uncrustify](#uncrustify)

These steps were performed on an OSX laptop, so you will need to look at the specific README files for Linux/Windows details. I also assume you have installed Package Control, which makes it trivial to install plugins.

## <a name="clang-complete">Clang-Complete</a>

OSX already comes with the clang compiler, and this plugin for SublimeText3 allows your source to be compiled on the fly and have diagnostic details made available.

Install [Clang-Complete](https://packagecontrol.io/packages/Clang-Complete)

There aren't any plugin settings to adjust, but you will want to set up either general clang settings, or project-specific clang settings. General settings may be include paths related to finding system libraries, however in my case I only set up project specific settings.

`Project -> Edit Project`

If you don't already have a `settings` key, create one, and then under that create a `cc_include_options` list of flags to pass to clang. Example:

```json
"settings": {
	"cc_include_options": [
		"-I/path/to/some/library/include"
	]
}
```

The setting is called "cc_include_options", but it is a bit misleading. Really it seems to just be any compiler flags you want to pass to clang. I have put `-D` macro defines, as well as being able to disable warnings, etc, etc.

Once you have your basic includes entered for your given project, you should review your key mappings. There is only one I cared about:

```json
{
    "command": "clang_goto_def", "keys": ["super+.", "super+g"],
    "context": [{"key": "selector", "operator": "equal", "operand": "source.c++"} ]
}
```

Adjust the "keys" sequence as needed. This gives you the ability to jump to definitions from a given symbol.

At this point, you should have code completion, and the ability to navigate through C++ symbols. You will know your code completion is working if the completion popup has more than just names, and actually has type information on the right side.

## <a name="switch-file-deluxe">Switch File Deluxe</a>

This is a pretty sweet and simple plugin that lets you switch between headers and source files. Before Sublime, I had been using Qt Creator for C++ Qt projects, and this functionality was built-in and mapped to the F4 key.

Install [Switch File Deluxe](https://packagecontrol.io/packages/Switch%20File%20Deluxe)

Once installed, all I had to do was map a key sequence:

```json
{ 
    "command": "switch_file_deluxe", "keys": ["f4"], 
    "args": {
        "extensions": [
            ".cpp", ".cxx", ".cc", ".c", "Qt.cpp", "Qt.h", 
            ".hpp", ".hxx", ".h", "_p.h", "_p_p.h", ".ipp", 
            ".inl", ".m", ".mm"
        ]
    } 
}
```

From any C++ header or source file, when you hit F4 you will switch to the corresponding header or source. If the matching file is ambiguous, you should be presented with a choice of files. Once you select the file, future uses of the key will immediately switch between the previously selected files.

## <a name="uncrustify">Uncrustify</a>

If you prefer your C++ source code to follow a particular style, then you can use the "uncrustify" tool to apply beautification based on a configuration file of rules.

First I installed uncrustify via homebrew:  `brew install uncrustify`

Then, install [Uncrustify](https://packagecontrol.io/packages/Uncrustify)

If your uncrustify binary is in a non-standard location, you can set the user settings for the plugin with `uncrustify_executable`. For setting up projects to match configs, I actually used the `uncrustify_config_by_filter` setting to set up a list of patterns:

```json
{
	"uncrustify_filtering_rule": 1,
	"uncrustify_config_by_filter": [
		{"/path/to/some/*/project": "/path/to/project/uncrustify.cfg"}
	]	
}

It helps to store your uncrustify.cfg within your project repo. Also, there is a tool I used for visually editing the rules for setting up an uncrustify config: [universalindent](http://universalindent.sourceforge.net/).
```

## Build System

On a per-project basis, you can set up your build commands. Going into `Project -> Edit Project`, I added something like the following to work with my particular build system for the project:

```json
{
  "build_systems": [
    {
      "file_regex": "(\\S*?):(\\d+):(\\d+): (error.*)",
      "name": "Default 'Project' Build",
      "shell": true,
      "shell_cmd": "./\\$USER/scripts/build_cmd arg1 arg2",
      "variants": [
        {
          "cmd": [
            "./\\$USER/scripts/build_cmd arg3 arg4"
          ],
          "name": "'Project' Build Debug",
          "shell": true
        }
      ],
      "working_dir": "/path/to/project/root"
    }
  ]
}
```

`file_regex` is a pattern specifying how to capture the filename, line number, column number, and error message from the output of the build command. This is useful to adapt to any kind of build system and have Sublime give you context about the errors in your view.

You specify the default build command, and then optionally a number of variants for the build system, such as debug builds or different options. Once this is entered you can now set your build system selection and use your build hotkey.

## Wrapping up

I don't do much debugging from within my editor (I am primarily an Editor+Terminal kind of developer), so I didn't go as far as to investigate Sublime options for debugging. But with a pretty minimal amount of configuration, I have a pretty lightweight and capable editor for working on C++ projects. And it is the same editor I use for working on Python and Go projects.

Got any other recommendations for making SublimeText an even more capable solution for C++ projects? Let me know!