+++
date = "2015-11-08T09:32:37+13:00"
draft = false
title = "Switching from Wordpress to Hugo"
type = "post"

+++

Since 2006, my small blog has existed as a Wordpress site. This worked really well for me, as it was easy to hit a one-click install from a hosting dashboard, and then be able to install plugins, themes, and updates, all from the Wordpress admin. Ultimately it was one less thing vying for my time as opposed to a custom site from scratch. With limited free time, and a choice of projects to work on, the last thing I really wanted to do was website design and maintenance. 

In addition to my Wordpress blog, for a while I had also been hosting other applications, such as a Redmine system for project management. Redmine, being a Ruby-on-Rails setup, was quite a memory hog. But that combined with Wordpress made sense to use some form of hosting. Eventually I stopped using Redmine and was left with just a Wordpress blog, hosted on a semi-VPS service, and paying $7 USD a month. Finally I realized this was silly and I should just switch to something that costs... maybe nothing?

## Hugo: A static site generator

I'm already a fan and user of the [Go language](http://golang.org), and so in following a number of the Go social communities, I discovered [Hugo](https://gohugo.io). A static site generator is an application that can take content in some original description (markdown in the case of Hugo), and renders final content by applying rules. This final content can then be hosted anywhere, without the need for server-side runtime support, such as Python/PHP/Go/Node/etc, because it does not require server-side processing to handle rendering the pages on the fly. This makes the site very portable and simple to deploy, and can be served out of aggressive caching. 

Another benefit of Hugo is that you don't need to futz with an in-browser editor while writing pages. Hugo comes with a built-in production-grade server, which provides developers the option of watching for any changes in content. When content changes, Hugo will render that content again, and also trigger an update of your local webpage by way of a connected websocket. I can sit comfortably within my code editor of choice (SublimeText), and write a post in markdown. Each time I save the file, my webpage immediately updates to reflect the changes. 

This post is primarily focused on my experience in switching to Hugo, and not attempting to provide an A-Z tutorial of using it, nor a comparison to any similar options in static site generation. Detailed documentation can be found on the official Hugo site. 

## Migrating from Wordpress to Hugo

I can't claim that the process of converting my Wordpress-based site to Hugo was 100% automatic, or even 90%. Let's say about 80%. But it was fairly painless. 

I started with the [Wordpress To Hugo Exporter](https://github.com/SchumacherFM/wordpress-to-hugo-exporter). Even though there were some claims that it didn't work 100% of the time, I actually had no problems using it with a semi recent 4.x install of Wordpress. I believe those that experienced issues might have had very large sites and encountered some kind of timeout issue. My site has relatively few posts, compared to others, and installing this plugin and exporting the site had worked right away. What you get as an end result is an archive of your site, converted into a basic Hugo project structure. All of your metadata from your posts are preserved in the Hugo format called ["Front Matter"](https://gohugo.io/content/front-matter/). This is a section of metadata that sits at the top of every markdown file, which the engine will use to build out your static site properly. For instance, the Front Matter that was generated for my post entitled ["Comparing performance of Qt smart pointer options"]({{< relref "2015-06-07-comparing-performance-of-qt-smart-pointer-options.md" >}}), looks like this:

{{< highlight yaml >}}
---
title: Comparing performance of Qt smart pointer options
author: Justin Israel
type: post
date: 2015-06-07
slug: comparing-performance-of-qt-smart-pointer-options
url: /2015/06/07/comparing-performance-of-qt-smart-pointer-options/
categories:
  - Code
tags:
  - benchmark
  - c++
  - pointers
  - qt
---
{{< /highlight >}}

This is YAML (the other supported format is TOML), and gives the engine information about the post that it can use to format the page, and to organize it into the site. You can see definitions of categories and tags (preserved from the Wordpress export), which also drives other aspects of the site like the menu system.

Once I had my new set of markdown files, I saw that they needed a tiny bit of cleanup. This probably has to do with the type of plugins I used in Wordpress to format code and embed media, which couldn't be translated to look exactly the way it should. But it was simple enough to go through each page and correct the bits of hyper-links and code formatting. A great thing about Hugo is that it comes with a production-quality web server, which you can use in development mode as you work on your site:

{{< highlight bash >}}
$ hugo server -w -D
{{< /highlight >}}

This starts the server, and tells it to run in "watch" mode, and to also render posts that are still in "draft" mode, so that I can see them in their unpublished state on the site. I can then check out my site right away, by hitting http://127.0.0.1:1313 in my browser. "Watch" mode means that the site is instrumented with a websocket connection to the server, and will automatically refresh when the server detects that content has changed (after the server automatically renders the content again). This is awesome, because I can type out content, hit save, and see the changes. This was a bit clunkier of a process in Wordpress, where I would type some stuff in a WYSIWYG editor and have to hit "save draft", and manually refresh my preview page in another tab (or maybe Wordpress auto-refreshed preview pages. I can't remember now). 

All-in-all the export and cleanup process was the most painless part. Most of the time was spent learning the new concepts of the Hugo system.

## Deployment

I'm going to start with describing the deployment process, since I find that to be one of the most appealing aspects of switching to Hugo. As I had mentioned, my previous solution was a paid hosted approach, in order to have a MySQL database, and a php-enabled dynamic site. My new approach brings my hosting expense down to __zero__. By using [Github Pages](https://pages.github.com/), I can treat my site as just another code repository, while serving it for free. You can see the final rendered static site, right here at [github.com/justinfx/justinfx.github.io](https://github.com/justinfx/justinfx.github.io). 

Generating your static site via Hugo is as simple as doing the following:

{{< highlight bash >}}
$ hugo
0 of 1 draft rendered
0 future content 
32 pages created
0 paginator pages created
45 tags created
3 categories created
in 195 ms
{{< /highlight >}}

This command causes Hugo to generate the site to the "public/" directory in the current working directory. If I had wanted to generate it to another location, I would just need to pass ```-d <dest>``` to tell Hugo to generate to another output location.

I actually symlink the "public/" subdirectory to point to my "justinfx.github.io" Github Pages local repo. What that means is that after running Hugo, and because it is managed via a git repository, I now only need to commit the new changes to my repo in order to update my public site. This is a very natural workflow for anyone that is comfortable with git repositories. And it also means that I automatically get revision control for any changes to my website.

## Understanding Hugo Concepts

The aspect that took the longest, in the switch to Hugo, was understanding the project structure, and how the layout of the site was generated. The main terms you will deal with upfront are things like: content, layouts, templates, types, archetypes, and static. To oversimplify what I have come to understand is that the Hugo project structure separates your content from your view, or the way that content ultimately is arranged in your site. You spend the most time getting your layout and types set up once, and then moving forward it becomes a simple matter of typing new markdown content, which gets seamlessly integrated into the layout.

### Configuration

The first thing you want to look at is your ```config.yaml``` file. There isn't much to it, and the default could be mostly correct for you. But I did have to change a few things to get it right. Mine looks like this:

{{< highlight yaml >}}
---
baseurl: "http://justinfx.com"
languageCode: "en-us"
title: "JustinFX.com"
DisqusShortname: "justinfx"
copyright: "Justin Israel"
pygmentsstyle: "friendly"

author:
    name: "Justin Israel" 

permalinks:
    blog: /:year/:month/:day/:slug/

taxonomies:
    tag: "tags"
    category: "categories"

params:
    < snip ... >
{{< /highlight >}}

Not every field is required. Probably the most important settings are the ```baseurl``` and ```title```. The rest of these settings control optional aspects of the site. You can see that I am setting my Disqus id, allowing Hugo to embed its integrated Disqus comment system into the posts. I'm also setting some options for how code syntax highlighting is displayed, and some definitions for organization and [permalinks](https://gohugo.io/extras/permalinks/). 

### Content

Under the "content/" directory of my project is where all my markdown files are stored. I have the following:

```
content/
    blog/
        <postA>.md
        <postB>.md
        <postC>.md
    page/
    	<pageA>.md
    	<pageB>.md
    wp-content/
    	...
```

"blog" and "page" reflect the concepts that came from Wordpress, where I had posts for the blog that would have comments and show up in the feed, and then pages which show up in menus and were not open for comments. The key difference in these two sections is the "type" parameter that is set in the Front Matter of the markdown files in each section. Blog posts have ```type: post```, while pages have ```type: page```. This becomes important in dictating how they are organized and rendered, when they need to line up with your layouts. Blog posts will be grouped and displayed [one way](/blog), with indexing and comments, while pages will be displayed another.

Within the markdown files, in addition to text and markdown formatting, you can also include [template logic](https://gohugo.io/templates/overview/), to be expanded at render time (running the ```hugo``` command). 

### Archetypes

At first, I had thought archetypes were something more complicated than they actually are. Really all they do is define a new "type template" so that when you create new content with ```hugo new <...>```, the new content will be created with type-specific defaults. For instance, I have an archetype defined that makes any markdown files created in my "content/blog/" location have the type "post":

{{< highlight bash >}}
$ ls archetypes/
blog.md

$ cat archetypes/blog.md
+++
type = "post"
draft = true
+++

$ hugo new blog/new_post.md
justinfx.com/content/blog/new_post.md created

$ cat content/blog/new_post.md
+++
date = "2015-11-21T09:20:47+13:00"
draft = true
title = "test"
type = "post"
+++
{{< /highlight >}}

You can see that a new draft post was created for me, and the Front Matter was pre-populated with the settings from the blog archetype. Just an easy way to set up new content really.

### Layouts

This was the hardest part of the learning curve for me. Layouts are the part of your codebase that specify how your content is turned into actual views in your static site. There is a correlation between the layout directories, and the type names we talked about previously. Because I have two types, "post" and "page", it means I will have the corresponding locations within the layouts directory. Hugo has a resolution order it will use, when figuring out which layouts to apply. First it will check if there is a direct match for the type, and then it will fall back on a special "_default" directory. Within these directories, you can specify what content should look like when it is viewed as:

* a single item - the actual blog post or page
* a list of items - an index of all blog posts, such as by date
* a summary of an item - like a preview of an item

For example, I have some layouts structured like this:

```
layouts/
    _default/
        list.html
        single.html
        ...
    page/
        single.html
    post/
        li.html
        single.html
        summary.html
    section/
        blog.html
```

"_default" will be the fallback for any particular types that don't explicitly have their own layout definitions. You can see that pages just define a specific single view. Posts define how they look when displayed in a list, or a single view, or their summary view. I also have a "section/" location defined, which results in the [/blog](/blog) endpoint on my site. 

"section/blog.html" looks like this:

{{< highlight html >}}
{{ template "chrome/head.html" . }}
<body>

{{ template "chrome/sidebar.html" . }}

<div class="content container">
  <h1 class="title">Blog Posts</h1> 
  <ul class="posts">
      {{ range .Data.Pages.GroupByDate "2006" }}
	    <h2>{{ .Key }}</h2>
	        <ul id="list">
	            {{ range .Pages }}
	                {{ .Render "li"}}
	            {{ end }}
	        </ul>
      {{ end }}
  </ul>
</div>

</body>
</html>
{{< /highlight >}}

Breaking this down, we have template directives that will include other templates that render the common header and sidebar. Then we set up how "/blog" will look when displaying a date-sorted list of all my posts. We loop over all the blog posts, grouped by the year they were created, then loop over each post in a group and render it using its "li" (list item) definition. This is the place where Hugo will resolve which view to use for list elements. Because we are dealing with "post" types, Hugo will find "layouts/post/li.html" first (matching "li" with "li.html", and use that to render each list item. 

"layouts/post/li.html" looks like this:

{{< highlight html >}}
<li>
      <span style="line-height: 1.0">
      {{if .Draft}}[draft] {{end}}<a href="{{ .Permalink }}">{{ .Title }}</a>
      <div class="post-item">
      	<span style="font-size: .75em">{{ .Date.Format "Mon, Jan 2, 2006" }}</span>
      </div>
      </span>
</li>
{{< /highlight >}}

This template is purely concerned with defining an ```<li>``` element. We handle optionally indicating if a post is still in draft state (only visible if we are running the hugo server with ```-D```), and then showing the title and date of the post.

Extra info on layouts and views can be found [here](https://gohugo.io/content/types/#defining-a-content-type:3d59665407ad3f08f6bcaef4686926f7) and [here](https://gohugo.io/content/types/#create-type-directory:3d59665407ad3f08f6bcaef4686926f7)

### Themes

Hugo supports [themes](https://gohugo.io/themes/overview/), and already has a sizable [gallery](http://themes.gohugo.io/) of existing themes to choose from. 

I had started out trying to just drop in different themes (placing them in the "themes" subdirectory, and activating them in the config file or via the command line), but I noticed that not every theme would render properly. I came to understand that themes still require a bit of integration, and are not simply a stylesheet. They define their own views which become part of the resolution order when Hugo looks them up at render time. Finally I just took the advise from [Nate Finch's Hugo post](http://npf.io/2014/08/hugo-is-awesome/), where he grabbed a theme that was closest to what he wanted and started tweaking it. I then started with Nate's modified theme (since I liked it), and also further tweaked it to match what I liked. This resulted in getting rid of an explicit "themes" location, and just integrating the templates directly into my "layouts/" location. 

### Shortcodes

[Shortcodes](https://gohugo.io/extras/shortcodes/) are sweet. They are very similar to Wordpress shortcodes provided by plugins, allowing you to create little functions that can take arguments and expand into reusable code. 

My shortcodes look like this:

```
shortcodes/
    lightbox.html
    vimeo.html
    youtube.html
```

And "vimeo.html" looks like this:

{{< highlight html >}}
<div class="js-video vimeo">
	<iframe src="https://player.vimeo.com/video/{{ .Get 0 }}?byline=0" 
	        width="640" 
	        height="360" 
	        frameborder="0" 
	        mozallowfullscreen 
	        allowfullscreen >
	</iframe>
</div>
{{< /highlight >}}

Now I have the ability to embed my vimeo media from within my markdown files, just by using the shortcode and passing the vimeo id as a parameter:

\{\{< vimeo 12067694 >}}

### Static files

Static files are pretty straightforward. It is just a "static/" location where everything underneath is served directly by your site, and does not go through a rendering process when you generate your site. This would be the place to store css, javascript, images, and other static content that simply needs to be served up in pages or urls. Themes can come with their own static locations, which will be integrated into the main static content when you render your site.

### Closing Thoughts

There is something quite satisfying about having the source of my site being so tangible, while being easy to update, and easy to deploy. I don't have to worry about a database, or hosting, or Wordpress site and plugin updates. I don't have to worry about security, since there are no dynamic pages or Wordpress vulnerabilities to guard against. I don't have to mess with backups and exports from a hosting service, since I check my entire site into github. 

Even for someone like me, that hates doing web front-end development, I didn't mind the minimal amount of css and html templates that needed tweaking. The end result is a site that is straightforward to manage, lightning fast to generate when deploying changes, and now costs me nothing to host.
