---
title: Write My Code For Me
author: Justin Israel
type: post
date: 2012-04-14
excerpt: |
  How do we respond to people who present questions that effectively translate to the statement: "Please write my code for me. Thanks!" ? 
  In the spirit of a very similar blog post, I decided to expand upon a specific area of that article...
slug: write-my-code-for-me
url: /2012/04/14/write-my-code-for-me/
categories:
  - Code
tags:
  - code
  - question
  - stackoverflow
---
Permalink &#8211; writemycode.net

_In the spirit of a [very similar blog post](http://whathaveyoutried.com), I decided to expand upon a specific area of that article&#8230;_

Consider a question like this:

> I want to do this. Create 10000 files, (filename can be just combination of time and random number). File size should be 4k. And I want to time this. say how many seconds it will take.
> 
> How can I do this on bash?
> 
> Thank you.

Obviously this person needs some assistance, and the question is very short and easy to understand. But the problem with this type of question is that there are really only two ways someone can approach the answer. Your options for an answer are either to just give the person the complete code snippet as they have requested, or fall back on a lengthy personalized tutorial.

**Short questions that primarily request a complete code example as an answer are counter-productive to code communities.**

##### Answer Option 1

As the topic of this article suggests, option 1 is &#8220;Write my code for me&#8221;. It might seem easy as a one-off situation to simply donate a working code snippet and get the person asking this question moving on their merry way, but really this is not helping them in the long run. They haven&#8217;t learned anything beyond the process of hitting a roadblock, then immediately going online to ask for a solution. Had this person included some references to what they have researched, and most importantly a code snippet representing what they have attempted, viewers of this question would have a basis for comment and potentially an answer pointing out where the person has gone wrong.

##### Answer Option 2

And in the other direction: providing a lengthy tutorial. We all want to help and teach, but this is just one of numerous questions floating out in the ether that requires a response. Can we really spare that much time for every single question like this to re-teach material that is most likely already documented in generalized contexts all across the internet? That would create quite a lot of redundant information simply because each person combines new components into a new question needing a new lesson. Really, every part of this question can be Googled quite easily. Why ask a community to do a new custom writeup for you?

Now, assuming we actually wanted to write a tutorial for this person asking the question. The problem at this point is where do we begin? The information provided doesn&#8217;t suggest that this person has a grasp on any part of the problem. So the tutorial answer might need to include:

  1. Bash and for-loops
  2. How to get the current time
  3. How to generate random numbers
  4. Creating files
  5. Populating new files to a specific size
  6. How to write a complete bash script, and time its execution.

Had they told us what they know how to do so far, and what aspect has them stuck, we could simply focus on one area and provide a good bit of knowledge to get them moving again. But right now this is just too much work to net a situation where they will learn something.

For those that are immediately inclined to provide the complete code snippet to solve the problem, where do we draw the line? What if the question being asked would need 10 lines of code? 20? 100? And if you are also interested in frequently helping people, would you be willing to provide 5 lines of complete code to 10 people a day, knowing that each person probably didn&#8217;t learn much? Furthermore, after having given this individual a quick answer, you have now rewarded their lazy behavior, and more than likely just encouraged them to repeat the bad habit again.

_Through a conversation between my coworker and I, some interesting metaphors were raised that I simply can&#8217;t resist from sharing&#8230;_

##### Vending-machine communities

**Put a question in the slot and pop out a solution.  **
  
Be it a traditional forum, an online discussion group, mailing list, or a trust and reputation based technical site like [stackoverflow.com](http://www.stackoverflow.com), these communities are driven by people, not machines. People have to take time to review content, and contribute their knowledge. We all work hard to acquire that knowledge, so lets all try and put some value on it in the form of the quality of our questions. A .25 cent answer is insulting. Treat your questions like they are costing you actual money that properly reflects the value of people&#8217;s knowledge. Code communities don&#8217;t work for you, and you don&#8217;t work for them. We are all here to help because we love it. Please don&#8217;t make us hate it, or feel like we are all just part of a big vending-machine.

##### **Toilet paper answers**

**Answers that can be used only one particular time for one particular situation.**
  
There is an insane amount of content on the internet. It&#8217;s hard enough sometimes to sift through the results of a vague Google search, let alone the content on our individual communities. When you ask a question that provides zero context, or proof of the extent of your current effort, then both the question and the answer are for the most part &#8220;throw aways&#8221;. If its going to be a persistent part of a community space, it should aim to benefit future support-seekers with similar situations. Referring to the example question above, someone going with Option 1 (&#8220;Write my code&#8221;) will end up providing an answer that will likely not help many people beyond this situation. Unless they too are looking for a way to do a for loop and create 10,000 4k dummy files and measure the execution time. The only way it would stand to benefit future visitors is if the answer did Option 2 and wrote a fully self-describing tutorial.

##### **Conclusion**

I can only speak for myself about what I might do. I consider myself to be the type that would go as far as to look at API docs for someone, and work out some pretty extensive examples on my local machine. I might even install libs that I don&#8217;t have or have never used before in an effort to provide assistance. But I need to be motivated to do so. It&#8217;s exciting for me to write out a page-length of information if I know it will help this person. But in a case like the above there is no show of effort and no context provided &#8212; just a person asking to have code written for them.

As I suggested already, we all want to help. Thats why we frequent these forums and sites. But we help these types of answer-seekers even more by withholding instant gratification.

And now&#8230; I direct you back to [whathaveyoutried.com](http://whathaveyoutried.com)

&nbsp;
