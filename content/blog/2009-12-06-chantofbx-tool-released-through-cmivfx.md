---
title: chanToFbx tool released through cmiVFX
author: Justin Israel
type: post
date: 2009-12-07
slug: chantofbx-tool-released-through-cmivfx
url: /2009/12/06/chantofbx-tool-released-through-cmivfx/
meta_keywords:
  - pyqt, qt, application, cmivfx, python, atom splitter, nuke, chan, fbx, flame, action, camera
categories:
  - FX
tags:
  - chan
  - cmivfx
  - nuke
  - pyqt
  - python
---
<img class="alignnone size-full wp-image-39" title="chan2fbx" src="/uploads/2009/12/chan2fbx.jpg" alt="chan2fbx" width="201" height="104" />

I had received a mailing list email from cmiVFX, where Chris Maynard was challenging the community to write a tool that could convert a nuke camera .chan file to a functional FBX format. This was apparently meant to compliment the new 3d camera tracker in NukeX. So I decided to take on the challenge.

<!--more-->

The tool started out as a command-line python script that would translate the chan files simple column-style output to fbx. But in testing specifically with Flame, Chris found that the fbx simply would not import all the channels properly. Thus, I was asked to integrate a solution by Georges Nakhle for converting the chan to an .action format, which is native to Flame. So with George&#8217;s .action code, Chris&#8217;s testing with the scaling in Flame, and the rest of my code, we seemed to have all the bases covered in getting the .chan file into a universal format.

Chris asked if I would wrap the tool into a GUI, to allow easier access to the few options the script provides. Using the cmiVFX.com website as a reference, I threw together a nice looking GUI in PyQt. It was fun because I got to really play around with CSS and skinning in PyQt, which is something I never really had to do before. I learned how much of a pain in the ass Palettes are, and how freaking simple the CSS route makes it to control the look or widgets.

Chris released it today: <del><a title="http://www.fxmogul.com/" href="http://www.fxmogul.com/" target="_blank" class="broken_link">http://www.fxmogul.com/</a></del>

* Update: This is no longer hosted through cmivfx, and has been posted on github, labeled &#8220;AtomSplitter&#8221;: <https://github.com/justinfx/AtomSplitter>

Currently there is a version for both OSX and Windows. I have been having some issues packaging the code under Linux, but I just need to really sit down and figure it out. The build is kinda larger than I hoped, but thats what I get for distributing PyQt <i class="fa fa-smile-o" aria-hidden="true"></i>

<div id="attachment_41" style="width: 590px" class="wp-caption alignnone">
  <img class="size-full wp-image-41" title="chan2FBX_screen" src="/uploads/2009/12/chan2FBX_screen.jpg" alt=".chan To FBX GUI (osx)" width="580" height="428" />
  
  <p class="wp-caption-text">
    .chan To FBX GUI (osx)
  </p>
</div>
