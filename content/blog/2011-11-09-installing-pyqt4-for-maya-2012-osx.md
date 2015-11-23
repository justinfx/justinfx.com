---
title: Installing PyQt4 for Maya 2012+ (osx)
author: Justin Israel
type: post
date: 2011-11-10
slug: installing-pyqt4-for-maya-2012-osx
url: /2011/11/09/installing-pyqt4-for-maya-2012-osx/
suf_meta_keywords:
  - maya, 2012, maya 2011, pyqt, pyqt4, qt, python, sip
meta_keywords:
  - maya, 2012, maya 2011, pyqt, pyqt4, qt, python, sip
categories:
  - Code
  - FX
tags:
  - 2012
  - maya
  - osx
  - pyqt
  - pyqt4
  - python
  - qt
---
This is a follow up post to my previous one on [Installing PyQt4 for Maya 2011](/2011/01/07/installing-pyqt-for-maya-2011-osx/)

Recently while putting together my next video tutorial for Python for Maya, I came to a section where I wanted to demo PyQt4 in Maya2012. But I was concerned that viewers would have to go through the complicated steps of building PyQt4. I noticed that other people have made available precompiled PyQt installers for windows (<a href="http://blarg.robertkist.com/?p=51" target="_blank" class="broken_link">here</a>) but I could not find any for OSX or linux. So I decided to put together a build.

I created a new project on github called MyQt4
  
<a href="https://github.com/justinfx/MyQt4" target="_blank">https://github.com/justinfx/MyQt4</a>

Its a Makefile for completely downloading and building PyQt4 for maya, and generating a .pkg installer. Hopefully someone can contribute improvements since I dont have a ton of experience writing makefiles, and also that someone might create a linux version.

Here is a link to the latest pkg build:

**Snow Leopard: **

  * Maya2011: <a href="https://dl.dropbox.com/u/34613220/MyQt4/MyQt4.7.4-maya2011-x64-osx-10.6.pkg" target="_blank">MyQt4.7.4-maya2011-x64-osx-10.6.pkg</a>
  * Maya2012: <a href="https://dl.dropbox.com/u/34613220/MyQt4/MyQt4.9.4-maya2012-x64-osx-10.6.pkg" target="_blank">MyQt4.9.4-maya2012-x64-osx-10.6.pkg</a>
  * Maya2013: <a href="https://dl.dropbox.com/u/34613220/MyQt4/MyQt4.9.4-maya2013-x64-osx-10.6.pkg" target="_blank">MyQt4.9.4-maya2013-x64-osx-10.6.pkg</a>

**Lion:  **

  * Maya2012: <a href="http://dl.dropbox.com/u/34613220/MyQt4/MyQt4.8.6-maya2012-x64-osx-10.7.pkg" target="_blank">MyQt4.8.6-maya2012-x64-osx-10.7.pkg</a>   (Thanks <a href="https://github.com/cgrebeld" target="_blank">Chris</a>!)
  * Maya2013: [MyQt4.8.6-maya2013-x64-osx-10.7.pkg](http://dl.dropbox.com/u/34613220/MyQt4/MyQt4.8.6-maya2013-x64-osx-10.7.pkg)

**Mountain Lion:**

  * <span style="line-height: 13px;">Maya2011:<a href="http://infixlabs.com/MyQt/MyQt4.7.4-maya2011-x64-osx-20130114.pkg"> MyQt4.7.4-maya2011<wbr />-x64-osx-20130114.pkg</a></span>

Here are builds other people have made:

  * Win 32bit: <http://blarg.robertkist.com/?p=51>{.broken_link}
  * Win 64bit:  [http://nathanhorne.com/?p=322  ](http://nathanhorne.com/?p=322)
  * Win 7 64bit Maya 2013: <https://www.box.com/s/1db3b0c903059ac89fc4>
  * Linux:  <http://www.kurianos.com/wordpress/?p=551>

