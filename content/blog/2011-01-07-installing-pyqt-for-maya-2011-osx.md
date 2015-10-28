---
title: Installing PyQt for maya 2011 (OSX)
author: Justin Israel
type: post
date: 2011-01-08
excerpt: The solution I arrived at, for successfully installing PyQt4 in Maya 2011, after collecting info from multiple sites...
url: /2011/01/07/installing-pyqt-for-maya-2011-osx/
meta_description:
  - Installing PyQt for maya 2011 (OSX)
meta_keywords:
  - maya, 2011, maya 2011, pyqt, pyqt4, qt, python, sip
categories:
  - Code
  - FX
tags:
  - maya
  - osx
  - pyqt
---
#### Update:

I am now hosting a built package for Maya2011: [MyQt4.7.4-maya2011-x64-osx-10.6.pkg](https://dl.dropbox.com/u/34613220/MyQt4/MyQt4.7.4-maya2011-x64-osx-10.6.pkg) And for Maya 2012+, see: [Installing pyqt4 for maya2012](/2011/11/09/installing-pyqt4-for-maya-2012-osx/)

* * *

Personally, when trying to run PyQt from within Maya 2009 / 2010 using the pumpThread method, I never had much luck. The best I ever got was the ability to bring up a dialog but not without locking up the UI, even though the pumpThread tool is meant to address that.

Anyways, when I found out Maya 2011 was rewritten based on Qt for the UI, I was really stoked. I saw the example video of being able to design a ui file in Designer, and just directly open it in a maya script, and all I could think about was designing Qt GUIs so much more easily now. Turns out that Maya 2011 didn&#8217;t actually ship with PyQt included for licensing reasons I&#8217;m sure. But it included documentation on how one could go about building PyQt for maya. Unfortunately I had tons of issues that caused maya to just crash when importing PyQt.

What I finally figured out was a mish-mash of information from the maya documention, and different forums and user groups. So I decided to make this easier on anyone having the same problems as I did, and just collect that information into one place. This process is for OSX. I&#8217;m sure most of it is probably still relevant to linux or win, except for the last parts with &#8216;install\_name\_tool&#8217;. You would just need to make sure to find the right Qt/PyQt/SIP packages for your OS.

##### Building PyQt4 for Maya 2011 on OSX

* * *

###### _**Update for Maya 2012**_

While Maya uses newer versions, it seems the versions from the 2011 install still work. But here they are anyways incase you want the newer version for 2012:

  * Autodesk modified [qt 4.7.1](http://images.autodesk.com/adsk/files/Qt-4.7.1-Modified_for_Maya.zip)
  * [sip 4.12.4](/wp-content/uploads/2011/01/sip-4.12.4.tar.gz)
  * [pyqt 4.8.6](https://dl.dropbox.com/u/34613220/MyQt4/src/PyQt-mac-gpl-4.8.6.tar.gz)

* * *

Make sure you have downloaded and installed the latest XCode from Apple. Its also included on your OSX installation disc.

###### Qt: Maya has a specific version of Qt built into it. This is Qt 4.5.3.

  1. Download:  [qt-mac-opensource-src-4.5.3.tar.gz](https://dl.dropbox.com/u/34613220/MyQt4/src/qt-mac-opensource-src-4.5.3.tar.gz)
  2. Extract: {{< highlight bash >}}
tar zxvf qt-mac-opensource-src-4.5.3.tar.gz
cd qt-mac-opensource-src-4.5.3{{< /highlight >}}

  3. Build and install: {{< highlight bash >}}
./configure -cocoa -arch x86_64 -debug-and-release \
  -no-phonon -no-phonon-backend -no-qt3support \
  -no-webkit -nomake docs -nomake examples -nomake demos \
  -nomake translations -no-rpath -no-framework
make -j 8
sudo make install{{< /highlight >}}

###### SIP: The maya docs recommend sip version 4.10

  1. Download this specific SIP:   [sip-4.10.tar.gz](/wp-content/uploads/2011/01/sip-4.10.tar.gz)
  2. Extract: <pre class="theme:twilight top-margin:20 bottom-margin:20 toolbar-overlay:false striped:false nums:false lang:sh decode:true">tar zxvf sip-4.10.tar.gz
cd sip-4.10</pre>

  3. Build and install: {{< highlight bash >}}
/Applications/Autodesk/maya2011/Maya.app/Contents/bin/mayapy \
  configure.py --arch=x86_64
make -j 8
sudo make install{{< /highlight >}}

###### PyQt4: The maya docs suggest PyQt 4.7

  1. Download this specific PyQt: [PyQt-mac-gpl-4.7.4.tar.gz](https://dl.dropbox.com/u/34613220/MyQt4/src/PyQt-mac-gpl-4.7.4.tar.gz)
  2. Extract: <pre class="striped:false nums:false lang:sh decode:true">tar zxvf PyQt-mac-gpl-4.7.3.tar.gz
cd PyQt-mac-gpl-4.7.3</pre>

  3. Set up some environment variables before building: {{< highlight bash >}}
export QTDIR=/usr/local/Trolltech/Qt-4.5.3
export PATH=/usr/local/Trolltech/Qt-4.5.3/bin:$PATH
export QMAKESPEC=/usr/local/Trolltech/Qt-4.5.3/mkspecs/macx-g++
export DYLD_LIBRARY_PATH=/usr/local/Trolltech/Qt-4.5.3/lib
{{< /highlight >}}

  4. Build and install: {{< highlight bash >}}
/Applications/Autodesk/maya2011/Maya.app/Contents/bin/mayapy \
  configure.py \
  LIBDIR_QT=/usr/local/Trolltech/Qt‐4.5.3/lib \
  INCDIR_QT=/usr/local/Trolltech/Qt‐4.5.3/include \
  MOC=/usr/local/Trolltech/Qt‐4.5.3/bin/moc \
  -w --no-designer-plugin
make -j 8
sudo make install
{{< /highlight >}}

  5. PyQt4 will now be installed into Maya&#8217;s python site-packages, BUT will be linked against the wrong Qt binaries. The maya docs have an annoying multi step set of commands but they don&#8217;t copy/paste nicely, so here is a for-loop you can use: {{< highlight bash >}}
for mod in Core Gui Svg OpenGL Xml
do 
  sudo find /Applications/Autodesk/maya2011/Maya.app/Contents/Frameworks/Python.framework/Versions/Current/lib/python2.6/site-packages/PyQt4 -name "*so" \
  -exec install_name_tool -change libQt${mod}.4.dylib @executable_path/Qt${mod} {} ;
done
{{< /highlight >}}

At this point you should be able to start up Maya and import and run PyQt from the script editor. You no longer need the pumpThread. Here is a test code snippet that I borrowed from here (the original had typos in it that I corrected)

{{< highlight python >}}
import maya.OpenMayaUI as mui
from PyQt4.QtCore import *
from PyQt4.QtGui import *

def getMayaWindow():
    ptr = mui.MQtUtil.mainWindow()
    return sip.wrapinstance(long(ptr), QObject)

class Form(QDialog):
    def __init__(self, parent=None):
        super(Form, self).__init__(parent)
        self.setWindowTitle('Test Dialog')
        self.setObjectName('mainUI')
        self.mainLayout = QVBoxLayout(self)
        self.myButton = QPushButton('myButton')
        self.mainLayout.addWidget(self.myButton)

global app
global form
app = qApp
form = Form(getMayaWindow())
form.show()
{{< /highlight >}}

It doesn&#8217;t seem like you even need the install of Qt 4.5.3 that we did at this point since we changed the links, unless you use another Qt module besides QtCore, QtGui, QtSvg, QtXml, QtOpenGL (such as QtNetwork), but this could be solved by copying over the missing libs to where Maya is expecting them. Example for copying over QtNetwork:

{{< highlight bash >}}
sudo cp /usr/local/Trolltech/Qt-4.5.3/lib/libQtNetwork.* 
    /Applications/Autodesk/maya2011/Maya.app/Contents/MacOS
sudo install_name_tool -change libQtCore.4.dylib @executable_path/QtCore 
    /Applications/Autodesk/maya2011/Maya.app/Contents/MacOS/libQtNetwork.4.dylib
{{< /highlight >}}

If you happen to have a mixed library environment like me, with more than one python lib location for code, and you see any funny errors while importing a module, just make sure that mayas python site-package is always in the front of the sys.path:

{{< highlight python >}}
sys.path.insert(0,'/Applications/Autodesk/maya2011/Maya.app/Contents/Frameworks/Python.framework/Versions/Current/lib/python2.6/site-packages')
{{< /highlight >}}

And there you have it. PyQt4 now installed in Maya 2011 under OSX.

* * *

References:

  * <http://images.autodesk.com/adsk/files/pyqtmaya2011.pdf>
  * [groups.google.com/group/python\_inside\_maya](http://groups.google.com/group/python_inside_maya/browse_thread/thread/cd7109604407cba2/618a61ccebf8ac10?lnk=raot&pli=1)
  * http://community.softimage.com/forum/autodesk-maya/python/pyqt-and-maya-pyqt-window-parented-to-maya-window/

