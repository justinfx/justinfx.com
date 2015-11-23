---
title: Mixing PyQt4 widgets and Maya UI objects
author: Justin Israel
type: post
date: 2011-11-21
slug: mixing-pyqt4-widgets-and-maya-ui-objects
url: /2011/11/20/mixing-pyqt4-widgets-and-maya-ui-objects/
meta_keywords:
  - maya, pyqt, UI, python, sip, api
categories:
  - Code
  - FX
tags:
  - api
  - maya
  - pyqt
  - python
  - sip
  - UI
---
A question came up in the [Maya-Python mailing list](http://groups.google.com/group/python_inside_maya) that I thought was a really good topic, and should be reposted.

Someone asked how you can create maya UI objects and embed them within your main PyQt application. Specifically he wanted to create a modelPanel and embed it so that he would have a camera view within his own PyQt window.

<!--more-->

Here is my example of how to achieve this&#8230;

{{< highlight python >}}
from PyQt4 import QtCore, QtGui

import maya.cmds as cmds
import maya.OpenMayaUI as mui

import sip


class MyDialog(QtGui.QDialog):

    def __init__(self, parent, **kwargs):
        super(MyDialog, self).__init__(parent, **kwargs)
        
        self.setObjectName("MyWindow")
        self.resize(800, 600)
        self.setWindowTitle("PyQt ModelPanel Test")

        self.verticalLayout = QtGui.QVBoxLayout(self)
        self.verticalLayout.setContentsMargins(0,0,0,0)

        # need to set a name so it can be referenced by maya node path
        self.verticalLayout.setObjectName("mainLayout")
        
        # First use SIP to unwrap the layout into a pointer
        # Then get the full path to the UI in maya as a string
        layout = mui.MQtUtil.fullName(long(sip.unwrapinstance(self.verticalLayout)))
        cmds.setParent(layout)

        paneLayoutName = cmds.paneLayout()
        
        # Find a pointer to the paneLayout that we just created
        ptr = mui.MQtUtil.findControl(paneLayoutName)
        
        # Wrap the pointer into a python QObject
        self.paneLayout = sip.wrapinstance(long(ptr), QtCore.QObject)

        self.cameraName = cmds.camera()[0]
        self.modelPanelName = cmds.modelPanel("customModelPanel", label="ModelPanel Test", cam=self.cameraName)
        
        # Find a pointer to the modelPanel that we just created
        ptr = mui.MQtUtil.findControl(self.modelPanelName)
        
        # Wrap the pointer into a python QObject
        self.modelPanel = sip.wrapinstance(long(ptr), QtCore.QObject)

        # add our QObject reference to the paneLayout to our layout
        self.verticalLayout.addWidget(self.paneLayout)

    def showEvent(self, event):
        super(MyDialog, self).showEvent(event)

        # maya can lag in how it repaints UI. Force it to repaint
        # when we show the window.
        self.modelPanel.repaint()
                    

def show():
    # get a pointer to the maya main window
    ptr = mui.MQtUtil.mainWindow()
    # use sip to wrap the pointer into a QObject
    win = sip.wrapinstance(long(ptr), QtCore.QObject)
    d = MyDialog(win)
    d.show()

    return d


try:
    dialog.deleteLater()
except:
    pass    
dialog = show()
{{< /highlight >}}

You need sip and the MQtUtil functions to convert between maya node paths and python Qbjects. Its the same idea as having to use those functions to get a reference to the maya MainWindow, in order to parent your dialog.
