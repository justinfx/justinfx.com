---
title: Go language for python programmers
author: Justin Israel
type: post
date: 2011-07-28 00:00:00 +0000
excerpt: A review of the programming language, Go, from the perspective of a python
  programmer.
slug: go-language-for-python-programmers
url: "/2011/07/28/go-language-for-python-programmers/"
meta_description:
- A review of the programming language, Go, from the perspective of a python programmer.
meta_keywords:
- Go, language, programming, python, server
thumbnail:
- http://www.whatsontianjin.com/ent_images/bbe2304a8d3e8bc8e365_1.JPG
categories:
- Code
tags:
- code
- go
- golang
- programming
suf_meta_keywords: []
suf_thumbnail: []
suf_meta_description: []
featured_image: []
suf_featured_image: []
enclosure: []

---
### 

### [<img class="alignleft" src="https://blog.golang.org/gopher/gopher.png" alt="" width="116" height="142" />](http://golang.org) Preface

I’ve been programming in python for over 5 years now, and I love the language a lot. I would look for any opportunity to accomplish my coding tasks using python, as opposed to learning new languages. PyQt4 for user interfaces, Django for web design, etc… And when python just isn’t an option, I have done my share of php, javascript, objective-c and so on.

Recently I started thinking that I should probably expand my programming knowledge a bit to make myself more marketable.<!--more--> While python is pretty key in the world of Visual Effects pipelines… so is C++. Having previously focused on being a Compositor and not a programmer, I had always told myself that if I needed to learn C++, then I was going too far in that direction. But now that I have been working in pipeline development for over 2.5 years, it has become clear that I really should explore the world of compiled languages.

Grabbing some books on C+, I dove in. Yuk. Why is it so freaking boring? It really does suck trying to learn C++ after having been so spoiled with python for so long. Its like taking a Aborigine and trying to turn him into a snooty English gentleman. Hmm…Is that right? Well whatever. Its completely disorienting. So many things that I never had to think about, like type declarations, memory management, pointers… But I kept on reading and learning.

Then I came across a post on Google+, by someone that actually works at Google, mentioning a language being developed in-house. So I started reading about Go @ [http://golang.org/](http://golang.org/)

### The Go Language

From the standpoint of a python programmer, Go feels like it sits right between C/C++ and python. You get the simpler syntax, but with the speed of a compiled language. Because Go has garbage collection, memory management isn’t a concern. I was never used to the code/compile/test/repeat pattern before, but it compiles so fast and is so easy to set up a project that it feels pretty natural. Go doesn’t require you to have header files and declare everything in advance, so banging out a simple program is quite fast and only slightly more overhead than writing a python script. Just have to add the step of compiling it. As far as the library, so far I have found everything pretty useful. And it seems that every time I search for a Go binding for something, I find one. I will go into more detail on that in a bit.

One thing that will make python programmers feel more at home is the type inference while creating a variable. While you can do something like this in Go:  `var myString string = "Foo"` you can also type the same thing like this: `myString := "Foo"`

The `:=` operator lets you initialize a new variable and makes the compiler figure out what type it should be, based on the return type of the right hand side.

Pointers are still kind of strange for python programmers, but Go is a lot more flexible about using them. You don’t always have to explicitly dereference them like `(*myPointer).myFunction()`. It just does it for you when you access member functions and attributes:  myPointer.myFunction().

Having no type inheritance is also a bit different as well. Instead of creating base abstract classes, and subclassing them, you only have structs… no classes. But you share functionality by using interfaces. An interface is just a definition of methods. If any object implements those methods, its consider that type of interface. This is something I have yet to really get into, since my current first project is more of a cmd program, rather than a pkg library. I’m sure I could be using interfaces already, but it hasn’t quite felt natural enough to incorporate as of yet.

A pretty crazy aspect of Go is its native support for concurrency using what they call “Goroutines”. The closest way I have been able to compare it to my experience in python is while using ZeroMQ for messaging. ZeroMQ promotes not only using its library for messaging, but also to replace issues with threads and locks. It has similar concepts in promoting concurrency. You divide your program up into its components and instead of sharing data structures between them, you communicate over channels (sockets in ZeroMQ). When you fire off a Goroutine, you aren’t waiting for it anymore. It can run and do its thing and you keep on going. You can then send data back and forth with channels, and even use them just for signaling, like saying “ok now exit”.

### Actual Usage

I’ve been writing a message server so far in python, using the [Tornado web server](http://www.tornadoweb.org/), along with some socket.io bindings called [TornadIO](https://github.com/MrJoes/tornadio), and also [ZeroMQ](http://www.zeromq.org/) for internal communication. So far its been working pretty well, but there are a lot of complex layers, with ZeroMQ sort of riding on top of Tornados ioloop. I decided to try and rewrite this server in Go. Turns out Go has a lot of built-in support for doing exactly what this python server was doing. The whole web-socket server functionality is part of the standard http library module. I quickly found [Go bindings for socket.io](https://github.com/madari/go-socket.io) and I was on my way.

It was quite fast to get the server to the point of doing global messages, but now I had to think about implementing the support for channel subscriptions. My first instinct was to go grab ZeroMQ and its bindings again, or to use Redis for the messaging, but then I was thinking “Shouldn’t I be able to do all this with Go’s concurrency?”. One thing that really helped me out was how fast everyone responds on the [golang-nuts discussion group](http://groups.google.com/group/golang-nuts). It was quickly pointed out by more than one person that I should definitely be able to accomplish the internal message routing purely in Go. And they were right. I just set up a “dispatcher” function and run it in a loop as a goroutine, and then pass messages in and out of it. The dispatcher manages its data structure, and no other part of the code accesses it directly.

So far, this Go server is turning out great, and I’m excited by the fact that its compiled and faster. I don’t have to distribute my source code now <i class="fa fa-smile-o" aria-hidden="true"></i>

### Performance

* Disclaimer: These numbers are just comparisons between what I built in python vs Go. I think the point is to reflect what I naturally came up with on my first pass at using Go, vs applying years of python experience. I’m dead sure my Go code isn’t written as efficiently as someone with more experience, which I think makes it even more interesting of a comparison.

Go comes with built in testing and benchmarking functionality. What I built was a client test that connects to a running server and rapid fires messages. It times how long it takes for a 150 byte message to be sent out, flow through the server, and come back to that client as been delivered. The gotest utility that is used to run the test code will run the test, and if it ran too fast to calculate timings, it will repeat the test over and over with larger iterations. When I first got my Go server working to where a client would send a message and it would just get broadcasted right back out to everyone, I ran a benchmark. Here are the results of my tests…

{{< rawhtml >}}
<table border="0" width="500px" cellspacing="0" cellpadding="0">
<tr>
    <th scope="col" align="left" width="200">TEST</th>
    <th scope="col" width="150">TIME PER MSG</th>
    <th scope="col" width="150">NET RESULT</th>
</tr>

<tr>
    <td width="200">Python (Tornado, …)</td>
    <td width="150">835314 ns/op</td> 
    <td width="150">&#8211;</td>
</tr>
<tr>
    <td width="200"> Go (barebones messaging) </td>
    <td width="150"> 107091 ns/op </td>
    <td width="150"> 7.8x faster </td>
</tr>
<tr>
    <td width="200"> Go (1-to-1 python port) </td>
    <td width="150"> 159823 ns/op </td>
    <td width="150"> 5.2x faster </td>
</tr>
<tr>
    <td width="200"> Go (weekly.12-01-2011) </td>
    <td width="150"> 76230 ns/op </td>
    <td width="150"> 11x faster </td>
</tr>
</table>
{{< /rawhtml >}}

The second Go test was after I finished implementing the same 1-to-1 feature set of the python version. I had thought the numbers would be dramatically impacted after adding the overhead of all the internal message routing, but the Go server still came out almost 5x faster than the python version. And this is from my first attempt at writing a Go program! I bet once I get a lot more solid with the language I can optimize this code a lot more.

### Conclusion

I’m pretty hooked on the language. I feel its the perfect option for a python programmer that wants some speed increases and simple concurrency, without having to learn something as intense as C++. Go is supposed to get faster and faster as they improve things like the goroutines, channels, and the garbage collector, so its a great time to jump in and start learning. Its really helped me understand more formal concepts that will probably make learning C++ even easier once I decide to go back to learning it <i class="fa fa-smile-o" aria-hidden="true"></i>

 
