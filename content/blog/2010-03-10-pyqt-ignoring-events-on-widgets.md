---
title: 'PyQt: Overloading/Ignoring events on widgets'
author: Justin Israel
type: post
date: 2010-03-10
slug: pyqt-ignoring-events-on-widgets
url: /2010/03/10/pyqt-ignoring-events-on-widgets/
meta_keywords:
  - pyqt, qt, python, widgets, signals
categories:
  - Code
tags:
  - pyqt
  - python
  - qt
---
You know when you have all these widgets laid out in your class, and you are hooking up all the connections, and you say &#8220;Aw dammit I have to subclass QLabel now just so make it ignore blahEvent&#8221;? You end up with all these little widget subclasses, where all they are doing is ignoring an event.

I noticed I was doing this a few times, in more than one of my classes, and finally got annoyed for the last time. I figured there had to be a simple way of just overloading the method on the normal object when I create an instance. Fortunately python considers everything objects and pretty much anything can be changed. So I did this:

{{< highlight python >}}
myLabel = QLabel()
myLabel.mousePressEvent = lambda event: event.ignore()
{{< /highlight >}}

Magic.

I have also had to make clickable widgets, such as QLabel:

{{< highlight python >}}
myLabel = QLabel()
myLabel.mousePressEvent = lambda event: myLabel.emit(SIGNAL("clicked"))
{{< /highlight >}}

Or if you had to do more than just a single statement:

{{< highlight python >}}
myLabel = QLabel()
def clickedEvent(event):
myLabel.emit(SIGNAL("clicked"))
# do other stuff
# do stuff
event.accept()
myLabel.mousePressEvent = clickedEvent
{{< /highlight >}}

I like this better than piling up subclasses that don&#8217;t do much.