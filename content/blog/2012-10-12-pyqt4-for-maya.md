---
title: 'Tutorial: PyQt4 UI Development for Maya'
author: Justin Israel
type: post
date: 2012-10-12
excerpt: |
  "cmiVFX just released the first of a series of training videos for PyQt4 UI Development for Maya, featuring Justin Israel. The base of this video is not just for Maya, but for ANY app structure that exists today. Maya is covered in the second half of the video to help people associate a stronger principal interfacing structure with one of today's most popular graphics packages. We would like to explicitly state that this video would be extremely helpful to a large range of VFX producers using different software pipelines. The goal of this video is to let the user interface their tools using PyQT4."
url: /2012/10/12/pyqt4-for-maya/
categories:
  - Code
  - FX
tags:
  - maya
  - pyqt4
  - python
  - tutorial
---
## [<img class="alignnone size-full wp-image-525" title="PyQt4 for Maya" src="/wp-content/uploads/2012/10/1350054625_Master.jpeg" alt="" width="800" height="450" />](http://www.cmivfx.com/tutorials/view/498/PyQt4+UI+Development+for+Maya)

# PyQt4 UI Development for Maya

Just released my 3rd python-based online training video through [cmiVFX.com](https://cmivfx.com/store/498-pyqt4+ui+development+for+maya)

## **Introduction**

<div>
  This tutorial is about learning PyQt4 python bindings for the Qt Framework, and how to introduce new UI elements to Maya as a platform.
</div>

<div>
  We discuss what comprises a &#8220;Framework&#8221; and a &#8220;GUI Framework&#8221;, and how Qt and PyQt4 work together.
</div>

<div>
</div>

&nbsp;

### **Getting Started With PyQt4**

<div>
  There are multiple ways of getting a working installation of PyQt4, both for the general system and for Maya. We look into these approaches to get your system up and running to begin working with PyQt4!
</div>

<div>
  We also talk about what is included, such as command line tools and applications, tips on how to test and learn the code, and how to structure a project.
</div>

<div>
</div>

&nbsp;

### **PyQt4 Fundamentals**

<div>
  Lets get crackin&#8217; and learn the basics!
</div>

<div>
  • What is a QObject? What is a QWidget? Common PyQt4 classes are explained in detail
</div>

<div>
  • Working with the Qt Designer application, to build a UI visually
</div>

<div>
  • Layouts: Making widgets resize elegantly and stay organized in your design
</div>

<div>
  • Coordinate space: How do widgets transform in your 2D screen space?
</div>

<div>
  • QApplication and the Qt Event Loop: The engine that runs your UI
</div>

<div>
  • Events, Signals, and Slots: How components communicate changes and how the application can respond to changes to make it dynamic
</div>

<div>
</div>

&nbsp;

### **General Examples**

<div>
  With an understanding of the framework components, we can begin working with fully functional stand-alone examples.
</div>

<div>
  • Common PyQt4 app template
</div>

<div>
  • Subclassing Widgets: Creating custom functionality to the existing classes provided by PyQt4
</div>

<div>
  • Dialogs: Raising dialog windows above existing windows, Modal vs Non-modal, and creating forms. We look at different ways to validate the data provided by the user, to these dialog forms.
</div>

<div>
</div>

&nbsp;

### **PyQt4 And Maya Introduction**

<div>
  Finally, some Maya action! Maya has a slightly different approach to using PyQt4…
</div>

<div>
  • How does the QApplication and event loop work?
</div>

<div>
  • Common Maya PyQt4 app template
</div>

<div>
  • Looking at the Maya API&#8217;s MQtUtil class
</div>

<div>
  • The sip module: Helping us translate between Maya&#8217;s Qt and our own PyQt4 code
</div>

<div>
</div>

&nbsp;

### **Replicating Maya&#8217;s UI Components**

<div>
  What better way to see examples of creating UI for Maya than to replicate some existing functionality? This gives us the opportunity expand with custom functionality
</div>

<div>
  In this chapter we will take two different UI components in Maya, and do a basic custom version of our own, and show to how link them up to Maya&#8217;s own callbacks.
</div>

<div>
</div>

<div>
  <strong>Some Features Of This Chapter Include</strong>
</div>

<div>
  • The QTableWidget
</div>

<div>
  • Model / View separation with QTreeView
</div>

<div>
  • Docking windows into the Maya interface
</div>

<div>
  • Mixing together PyQt4, the Maya API, Maya commands, and callbacks
</div>

<div>
  • Sorting model data
</div>

<div>
</div>

&nbsp;

### **Customizations**

<div>
  A button can be a button, and a slider might look alright in its stock form, but sometimes we want to customize the look of our widgets. This chapter introduces multiple ways of achieving custom looks to our components
</div>

<div>
  • Stylin&#8217; Stylesheets: Use CSS-like syntax for applying style sheets to widgets
</div>

<div>
  • Painting By … Paint events: For even more control, we can tell a widget exactly how to draw itself on the screen. We will look at two different examples of how to use custom painting.
</div>

&nbsp;

### <span style="color: #ff6600;">Previous cmiVFX tutorials:</span>

  * [Intro to Python for Maya &#8211; Vol 1](https://cmivfx.com/store/320-python+introduction+vol+01+-+maya)
  * [Python for Maya  &#8211; Vol 2](https://cmivfx.com/store/328-python-for-maya-vol-02)

