---
title: Apple Macbooks and Unexplainable LCD Screen Flicker
author: Justin Israel
type: post
date: 2011-12-05
excerpt: I had just received my brand new MacBook Pro a few days ago. Amazing machine. Probably the best laptop I have ever laid my hands upon. It was the early Feb 2011 model so I got a crazy good deal. But something caught my eye that I just had to investigate...
url: /2011/12/05/apple-macbooks-and-unexplainable-lcd-flicker/
meta_keywords:
  - macbook pro, macbook air, flicker, lcd, display, artifact, inversion, text, osx, lion
thumbnail:
  - http://www.justinfx.com/blog/wp-content/uploads/2011/12/bmacbook_flicker.png
categories:
  - Random Stuff
tags:
  - mac
  - macbook
  - osx
  - troubleshooting
---
  
<img src="/wp-content/uploads/2011/12/bmacbook_flicker.png" alt="" title="Macbook Pro flicker" width="490" height="268" class="alignnone size-full wp-image-395" />
    
I had just received my [brand new MacBook Pro](http://www.bhphotovideo.com/c/product/756789-REG/Apple_MC721LL_A_15_4_MacBook_Pro_Notebook.html) a few days ago. Amazing machine. Probably the best laptop I have ever laid my hands upon. It was the early Feb 2011 model so I got a crazy good deal. But something caught my eye that I just had to investigate&#8230;

I&#8217;m a bit obsessive when it comes to small issues that I can&#8217;t resolve, and this is just still bothering me. I noticed that when I smooth scroll (trackpad or smooth wheel logitech mouse) on such content as webpages, or Mail, that there is a flicker in the display of text and context. It depends on the size and orientation of the content, whether it will flicker more or less. My eyes just couldn&#8217;t ignore it and I figured it couldn&#8217;t possibly be normal functionality. Thus, step one of my problem solving began: Google search.

My search turned up a number of similar complaints to both [MacBook Pro](http://www.apple.com/macbookpro/) and [Air](http://www.apple.com/macbookair/) models, such as this discussion: https://discussions.apple.com/thread/2645516?start=0&tstart=0
  
It seemed the problem was not just limited to my specific early 2011 Macbook Pro. Suggestions ranged from resetting the PRAM (holding cmd+option+p+r for a couple of reboot cycles), to toggling the screen through display resolutions, to adjusting brightness of the display. Nothing seemed to make much of a difference to me.

#### Inversion (pixel-walk)

Googling also turned up a reference to this site, which offers various types of LCD tests: <http://www.lagom.nl/lcd-test/inversion.php>

According to this site, its normal to have a slight flicker in one box. And heavier flickering suggests voltage alignment issues in the LCD display. Using this test, I tried it on a number of Apple product configurations. Here is a collection of my findings:

<table class="sample">
  <tr>
    <th width="170" scope="col">
      Device
    </th>
    
    <th width="137" scope="col">
      Display
    </th>
    
    <th width="241" scope="col">
      Scrolling
    </th>
    
    <th width="233" scope="col">
      Not Scrolling
    </th>
  </tr>
  
  <tr>
    <td>
      Macbook Pro (early 2011)
    </td>
    
    <td>
      15" 1440&#215;900
    </td>
    
    <td>
      Heavy multi-color flickering in all boxes
    </td>
    
    <td>
      At least 2 boxes always lightly flickering
    </td>
  </tr>
  
  <tr>
    <td>
      Mac Pro MacPro4,1 (2009)
    </td>
    
    <td>
      24" Cinema Display
    </td>
    
    <td>
      Light amount of flickering in all boxes
    </td>
    
    <td>
      No flicker
    </td>
  </tr>
  
  <tr>
    <td>
      Mac Pro MacPro5,1 (2010)
    </td>
    
    <td>
      27" LED Cinema Display
    </td>
    
    <td>
      No flicker
    </td>
    
    <td>
      Very light flicker in box 7a
    </td>
  </tr><caption> 
  
  <a href="http://www.lagom.nl/lcd-test/inversion.php">Inversion (pixel-walk)</a> test results on LCD displays<br /> </caption>
</table>

#### Apple Support

After speaking to support over the phone, they suggested that I go into the store for more help. When I got to the store and started speaking with a tech at the Genius Bar, he had never heard of this issue before. But once I showed him an example on both news.google.com, and in my Apple Mail, he definitely acknowledged that its noticeable. He then went off into the back to research the issue a bit.
  
When the tech came back he said that he had found no outstanding information from Apple about this issue. A second tech even came and looked at the issue, and had no explanation for it.
  
I went around the store and checked web page scrolling on 13&#8243; MacBook Airs, and 13&#8243;, 15&#8243;, and 17&#8243; MacBook Pro models, with and without the higher resolution LCD display options. All models exhibited the same flicker during scrolling. My final recommendation from the stumped Apple tech was that it could be an issue with Lion and its rendering of fonts, or whatever, and that I could either return my laptop, or hold out for some kind of fix from Apple. Basically, no idea.

My question is&#8230; Am I being overly sensitive to this display flicker? I figure I can&#8217;t be the only one, as per the discussion lists of other users that notice the problem. Some of my friends with the same laptop said they have never really noticed until I pointed it out. I just wonder why such a fantastic laptop would exhibit this visual artifact, and whether it is something I should just accept as being normal? 


