---
title: Getting started with XBOX 360 Kinect on OSX
author: Justin Israel
type: post
date: 2012-06-21
excerpt: A recent project of mine involves research and development with an XBOX 360 Kinect Sensor. Being a python guy, I started searching for python bindings to some OSX-supported framework. When you just get started looking into this area it can be a little confusing. There are a number of layers to the software stack to enable one to accomplish anything meaningful. This is just a short and general blog post outlining the basics of what I have discovered thus far, to help anyone else that might also be getting started.
url: /2012/06/21/getting-started-with-xbox-360-kinect-on-osx/
categories:
  - Code
  - FX
tags:
  - 3d
  - kinect
  - osx
  - python
---
A recent project of mine involves research and development with an XBOX 360 Kinect Sensor. Being a python guy, I started searching for python bindings to some OSX-supported framework. When you just get started looking into this area it can be a little confusing. There are a number of layers to the software stack to enable one to accomplish anything meaningful. This is just a short and general blog post outlining the basics of what I have discovered thus far, to help anyone else that might also be getting started.

At the lowest level, you need a driver. Something that can talk to the USB device that is the Kinect sensor. When you purchase the XBOX Kinect for Windows version of the sensor, and you are going to be developing on windows, much of this whole stack is provided to you by way of the Kinect SDK. But for the open source folks with the standard XBOX 360 sensor, you need to piece together your own solution.

Two drivers that I have discovered thus far:

  * [OpenKinect](http://openkinect.org/wiki/Main_Page)
  * [PrimeSense Sensor](https://github.com/avin2/SensorKinect)

I had started OpenKinect (libfreenect) because it comes with a [python wrapper](https://github.com/OpenKinect/libfreenect/tree/master/wrappers/python) included. There were a few dependencies (I will talk about specific build steps in just a moment), but once I got this installed I was able to fire up the included  glview app and see both depth and rgb data streaming in from my sensor. The role of these drivers is to provide simply the basic streams. That is, the depth, rgb, audio, and a few other sensor data streams. If your goal is to start tracking players, seeing skeletons, and registering gestures, the drivers are not enough. You would be required to make your own solution from this raw data at this phase in the game.

You would now want to look into middleware that can take the raw data and provide to you an API with higher level information. This would include finding users in the scene for you, tracking their body features, and giving you various events to watch for as the data streams.

Being that my goal was to have python bindings, I found my options to be much more limited than if I were going to be developing in C++. Wrappers have to exist for the framework you want. This is where my research really started ramping up. I spent a few days dealing wtih compiling issues, as well as having an actual bad power adapter that had to be exchanged. But all said and done, here is what I have settled on thus far&#8230;

  1. Driver: [PrimeSense Sensor](https://github.com/avin2/SensorKinect)
  2. [OpenNI Framework](https://github.com/OpenNI/OpenNI)
  3. A recent project of mine involves research and development with an XBOX 360 Kinect Sensor. Being a python guy, I started searching for python bindings to some OSX-supported framework. When you just get started looking into this area it can be a little confusing. There are a number of layers to the software stack to enable one to accomplish anything meaningful. This is just a short and general blog post outlining the basics of what I have discovered thus far, to help anyone else that might also be getting started.

At the lowest level, you need a driver. Something that can talk to the USB device that is the Kinect sensor. When you purchase the XBOX Kinect for Windows version of the sensor, and you are going to be developing on windows, much of this whole stack is provided to you by way of the Kinect SDK. But for the open source folks with the standard XBOX 360 sensor, you need to piece together your own solution.

Two drivers that I have discovered thus far:

  * [OpenKinect](http://openkinect.org/wiki/Main_Page)
  * [PrimeSense Sensor](https://github.com/avin2/SensorKinect)

I had started OpenKinect (libfreenect) because it comes with a [python wrapper](https://github.com/OpenKinect/libfreenect/tree/master/wrappers/python) included. There were a few dependencies (I will talk about specific build steps in just a moment), but once I got this installed I was able to fire up the included  glview app and see both depth and rgb data streaming in from my sensor. The role of these drivers is to provide simply the basic streams. That is, the depth, rgb, audio, and a few other sensor data streams. If your goal is to start tracking players, seeing skeletons, and registering gestures, the drivers are not enough. You would be required to make your own solution from this raw data at this phase in the game.

You would now want to look into middleware that can take the raw data and provide to you an API with higher level information. This would include finding users in the scene for you, tracking their body features, and giving you various events to watch for as the data streams.

Being that my goal was to have python bindings, I found my options to be much more limited than if I were going to be developing in C++. Wrappers have to exist for the framework you want. This is where my research really started ramping up. I spent a few days dealing wtih compiling issues, as well as having an actual bad power adapter that had to be exchanged. But all said and done, here is what I have settled on thus far&#8230;

  1. Driver: [PrimeSense Sensor](https://github.com/avin2/SensorKinect)
  2. [OpenNI Framework](https://github.com/OpenNI/OpenNI)
  3. [OpenNI Modules](http://www.openni.org/Downloads/OpenNIModules.aspx) for OpenNI
  4. [PyOpenNI](https://github.com/jmendeth/PyOpenNI) python bindings

#### <span style="text-decoration: underline;">Install Details</span>

##### **Install homebrew (package manager)**

<http://mxcl.github.com/homebrew/>

##### **Install build tools**

<pre>brew install cmake
brew install boost</pre>

##### **Install python2.7**

<pre>brew install python --framework</pre>

##### **Suggestion: virtualenv Environment**

This is not a requirement. But I recommend using virtualenv to set up an environment that specifically uses python2.7 so that you don&#8217;t have to fight with mixed dependencies and versions.

Create a virtualenv called &#8220;kinect&#8221;

{{< highlight bash >}}
pip install virtualenv
virtualenv --no-site-packages -p python2.7 kinect
cd kinect
source bin/activate
{{< /highlight >}}

##### **Install libusb (patched version)**

There is a special patched version of the libusb library, in the form of a homebrew formula.

<pre class="lang:default decode:true">git clone https://github.com/OpenKinect/libfreenect.git</pre>

Now copy platform/osx/homebrew/libusb-freenect.rb -> /usr/local/Library/Formula/

```
brew install libusb-freenect
```

##### **Install SensorKinect drivers**

```
git clone https://github.com/avin2/SensorKinect.git
```

Then uncompress Bin/SensorKinect093-Bin-MacOSX-v*tar.bz2

```
sudo ./install.sh
```

##### **Install OpenNI framework**

  1. Go here: <http://www.openni.org/Downloads/OpenNIModules.aspx>
  2. Download Unstable Binary for MacOSX
  3. ```sudo ./install.sh```

##### **Install NITE middleware (for OpenNI)**

  1. Go here: <http://www.openni.org/Downloads/OpenNIModules.aspx>
  2. Download Unstable MIDDLEWARE of NITE for OSX
  3. ```sudo ./install.sh```

##### **Install PyOpenNI**

Be aware that on OSX, PyOpenNI requires a framework build of python 2.7+ and that you must build it for x86_64 specifically. Also, I was having major problems with cmake properly finding the python includes location. I had to suggest a fix, so [please see here for the necessary corrections](https://github.com/jmendeth/PyOpenNI/issues/16#issuecomment-6515678). I have referenced a patched fork of the repository below.

{{< highlight bash >}}
export CPPFLAGS="-arch x86_64"
git clone git://github.com/justinfx/PyOpenNI.git
mkdir PyOpenNI-build
cd PyOpenNI-build
cmake -D PYTHON_INCLUDE_DIR=/usr/local/Cellar/python/2.7.3/Frameworks/Python.framework/Headers ../PyOpenNI
make
{{< /highlight >}}

copy the lib/openni.so module to the python2.7 site-packages

#### <span style="text-decoration: underline;">Examples</span>

Once you have everything installed, you can try out the examples that are included both in the NITE source location that you downloaded and also in the PyOpenNI source location:

  1. NITE/Samples
  2. PyOpenNI/examples

<div>
  I also tried out ofxKinect (<a href="https://github.com/ofTheo/ofxKinect">github.com/ofTheo/ofxKinect</a>) on the side, which is an addon for  <a href="http://www.openframeworks.cc/">OpenFrameworks</a>. This is kind of a separate path than the OpenNI stack. I would say its more like an advanced offering of libfreenect. Using the included example, I recorded a 3D point cloud that is built on the fly from the RGB and depth data:
</div>

<div>
</div>

&nbsp;

{{< youtube vICLgxnZ1Bs >}}

&nbsp;
