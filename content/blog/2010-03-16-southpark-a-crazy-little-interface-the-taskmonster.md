---
title: 'SouthPark: A crazy little interface. The TaskMonster.'
author: Justin Israel
type: post
date: 2010-03-16
slug: southpark-a-crazy-little-interface-the-taskmonster
url: /2010/03/16/southpark-a-crazy-little-interface-the-taskmonster/
meta_keywords:
  - southpark, qt, pyqt, python, sqlalchemy, twisted, taskmonster, skinning, theme, stylesheet, application, css
enclosure:
  - |
    |
        http://justinfx.com/public/video/taskMonster2.mov
        0
        video/quicktime
        
categories:
  - Code
tags:
  - pyqt
  - python
  - qt
  - southpark
---
I get to do a lot of interesting applications at SouthPark. This one in particular was the most challenging use of PyQt that I have experienced to date.

The backstory&#8230;.
  
The art department wanted a tool to help them track assigned tasks, the progress, and to share media and notes associated with the tasks. Furthermore, they wanted to be able to skin the interface with custom graphics to make it their own.

Progress&#8230;
  
During the winter break (about a month) I was able to come up with version 1.0 of TaskMonster. It was written in python, using PyQt for the UI, sqlalchemy to talk to the database, and twisted for the client/server communication. Each client app sends messages to a small server daemon which in turn tells the rest of the clients about the updates.

Version 2.0 Alpha&#8230;
  
Tony Postma, from the art department, put together a design in Corel which I could hopefully implement in the UI.  It called for the users to be represented as little pods in a circle around the supervisor, Adrien Beard. And each user pod could be clicked and rotated into place, in order to view that persons tasks.
  
After a bunch of testing I was able to design a rotating widget that could dynamically lay out N widgets around it in a circle, track their position, and jump to any other widget. I was also able to break down the corel->illustrator file, into a combination of SVG and PNG images, and skin the UI via CSS stylesheets.
  
Its currently an alpha release. I guess I need to really learn how to control the widget painting, to make it faster. Never had to do this much before.

What I came up with&#8230;  

<a href="/uploads/2010/03/taskMonster2.jpg" rel="lightbox[71]"><img class="alignnone size-large wp-image-72" title="taskMonster2" src="/uploads/2010/03/taskMonster2-1024x724.jpg" alt="" width="663" height="469" /></a>

{{< vimeo 143717524 >}}