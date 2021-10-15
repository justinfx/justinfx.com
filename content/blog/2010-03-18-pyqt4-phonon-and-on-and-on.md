---
title: 'PyQt4: phonon… and on… and on'
author: Justin Israel
type: post
date: 2010-03-19
slug: pyqt4-phonon-and-on-and-on
url: /2010/03/18/pyqt4-phonon-and-on-and-on/
meta_keywords:
  - pyqt4, qt, phonon, plugin, pyqt, phonon_backend, macdeployqt, custom, dylib, application
categories:
  - Code
tags:
  - phonon
  - plugin
  - pyqt4
  - qt
---
&#8220;App&#8217;ing up&#8221; PyQt&#8230; ugh.

One of biggest problems with PyQt is distributing it in a stand-alone package. Even worse&#8230; wanting to make your Qt plugins still function (Phonon, jpeg, etc). At work I constantly had this battle, along with my co-worker Tory. She actually has a long-standing issue with this, and had to resort to workarounds or half fixes. [<span style="color: #3366ff;">Here is Tory&#8217;s original post regarding the issue</span>](http://www.toryhoke.com/2009/03/03/pyqt4-i-hate-you/) .

I would see an error similar to this when trying to package up and run an app using the Phonon module.
  
```
WARNING: bool Phonon::FactoryPrivate::createBackend() phonon backend plugin could not be loaded
```

Running `macdeployqt myAppName.app` does add things like the jpeg plugin, but never seemed to fix the Phonon issue. I finally decided to randomly look online for a solution, again, last week. What I found was a partial solution, followed by me trying one more thing and bam&#8230;it worked! Video playback from my .app standalone package.

Here is what I did &#8230;
  
(btw you might have to modify the location of the plugin, since I happen to be using OSX)

  1. In your setup.py file, which is used for py2app, py2exe, or similar&#8230; add this to the DATA_FILES list, so that it looks as such:

    {{< highlight python >}}
    # This will put the phonon backend plugin into the 
    # RESOURCES folder in the app.
    DATA_FILES = [('phonon_backend', [
      '/Developer/Applications/Qt/plugins/phonon_backend/libphonon_qt7.dylib'
    ])]
    {{< /highlight >}}
    
  2. Package up your application via py2app / py2exe / etc.
  3. If you are on OSX, use macdeployqt on the app: `>>> macdeployqt myApp.app`
  4. Go into the app that was created (show package contents if you are on a mac), and move the phonon_backend directory FROM the Resources directory TO the PlugIns directory (which should be at the same level as Resources).

That should be it!
