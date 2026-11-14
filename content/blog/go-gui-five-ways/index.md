+++
title = "One Go App, Served Five Ways"
date = "2026-11-10T00:00:00+13:00"
type = "post"
slug = "go-gui-five-ways"
draft = false
categories = ["Code"]
tags = ["go", "golang", "gui", "benchmark"]
excerpt = "I had coding agents build the same data-heavy app with Fyne, Cogent Core, Wails, GoTTH and MIQT, then measured what a user would actually notice."
summary = "I had coding agents build the same data-heavy app with Fyne, Cogent Core, Wails, GoTTH and MIQT, then measured what a user would actually notice."
featureAlt = "Five plates on a dark table, each holding a small app window plated like a fine dining course."
+++

I've been keeping an eye on the Go GUI options for a long time. Most of my GUI background is Qt, in both Python and C++, and I've written some decently sized apps with it at Wētā FX. So when I first started using Go, what I really wanted was just another Qt. Back then that came with some pretty major bootstrap costs. Over the years I dabbled with other options, like an older Fyne, [imgui](https://github.com/ocornut/imgui), [nuklear](https://github.com/Immediate-Mode-UI/Nuklear) and [iup-go](https://github.com/gen2brain/iup-go). Some of them looked like crap out of the box, others came with heavy cgo dependencies, and imgui and nuklear are immediate mode, which I never felt as comfortable with. The one I ended up liking the most was iup-go. Like Qt, it's retained mode, which is what I'm most comfortable with. But none of them ever fully solved every area for me.

More recently a colleague told me he thinks all of our GUIs should just be web apps. I get why: a web app only needs a browser, with nothing to install or launch locally. We have people with all levels of technical background, on both macOS and Linux, and with a web app neither the platform nor their environment matters. Some of our tools are perfectly happy being full web apps. But a lot of what we build still needs to act as the user on their own machine. We have a lot of NFS shared filesystems that often need to respect user and group permissions. We might need to launch a particular image viewer in a particular environment to look at a sequence of images, or read headers and metadata from media as that user, or deal with custom auth where files have to be read as a given user. A web app on a server somewhere runs as the server, not as you. So what do you do with the apps that still need that kind of access?

There's another reason I've avoided the web route for so long: I hate writing web frontend code. But with coding agents that doesn't really matter anymore. If an agent can do the frontend work, I can focus on the Go business logic, and I feel a lot more free to pick whichever option actually has the best strengths for the target. That seemed like a good excuse to look at the options again.

I went in with a couple of assumptions. I thought maybe one of the Go-native toolkits, like Fyne, had a good web story by now. And I'd heard a lot about how popular Wails has been, so I half expected it to come out as the strongest contender. Rather than just reading docs and READMEs, I wanted to build the same app with each option and see what actually happened.

This post isn't trying to crown a winner, and it isn't a code review of each framework. It's a comparison of the same goals across five different approaches, and the strengths and weaknesses that showed up along the way, organized around the questions I actually ask when picking one. Do I have a lot of data? Do I need cgo? Do I need web, native, or both?

{{< toc >}}

## The Setup

### Five approaches to the same app

I picked five approaches that cover pretty different ideas of what a Go GUI is:

- **[Fyne](https://fyne.io)** (v2.8.2): Go all the way down. It draws its own widgets, and can also compile to WASM for the browser.
- **[Cogent Core](https://www.cogentcore.org/core)** (v0.5.27): same general idea as Fyne. Its own drawn widgets, and a WASM build for the browser.
- **[Wails](https://wails.io)** (v3.1.0): a Go backend with a web frontend in a native WebView. I paired it with [Svelte](https://svelte.dev) 5, [Tailwind](https://tailwindcss.com) and [shadcn-svelte](https://shadcn-svelte.com). It can also run as a plain web server.
- **GoTTH**: not really a framework, more of a stack. [templ](https://templ.guide), [htmx](https://htmx.org), Tailwind and [shadcn-templ](https://github.com/axadrn/shadcn-templ), served by a Go web server, with [webview_go](https://github.com/webview/webview_go) as a thin desktop shell.
- **[MIQT](https://github.com/mappu/miqt)** (v0.15.0, on [Qt](https://www.qt.io) 6.12): Go bindings for Qt Widgets. This is the "just give me another Qt" option, and it has no browser target at all.

<div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(240px,1fr));gap:12px">
{{< zoom src="desktop-dark-fyne.webp" alt="Fyne: the app in its dark theme, table view with the detail form open." caption="Fyne" >}}
{{< zoom src="desktop-dark-cogent.webp" alt="Cogent Core: the app in its dark theme, table view with the detail form open." caption="Cogent Core" >}}
{{< zoom src="desktop-dark-wails.webp" alt="Wails: the app in its dark theme, table view with the detail form open." caption="Wails" >}}
{{< zoom src="desktop-dark-gotth.webp" alt="GoTTH: the app in its dark theme, table view with the detail form open." caption="GoTTH" >}}
{{< zoom src="desktop-dark-miqt.webp" alt="MIQT: the app in its dark theme, table view with the detail form open." caption="MIQT" >}}
</div>

*The same app five ways, all in their dark theme on macOS.*

MIQT being desktop-only isn't a mark against it. It shows what you get when you drop the web requirement.

{{< svg "uploads/2026/11/process-routes.svg" "Where the Go process runs, and as whom, for each route." >}}

### One spec, one shared core

Every app implements the same spec. It's a fairly demanding inventory-style app: a 1M-row table with sorting, searching and CSV export; custom cells for a stock bar, a status badge and star ratings; a 1M-item card view; a detail form; preferences and theming; a background job with progress; native menus, dialogs and notifications; and showing which user the app is running as.

The data doesn't come from the framework. Every app talks to the same small Go module, with no dependencies, that serves the million rows with 50 ms of simulated latency, hands them out in 200-row pages, and keeps a small cache. The apps had to use it unchanged. That keeps the data side the same for everyone, so the differences we see are about the GUI approach and the glue code needed to use it.

### Why I let agents write the apps

Each app was built by an AI coding agent (Claude Code, using Sonnet), working alone from a brief written just for its framework. The briefs were generated from the one spec, with a check that nothing about one framework leaked into another's brief. The agents weren't told how the results would be judged.

I'm sure an expert in each framework would have written a better app. But I think that makes it a more honest comparison of what you get on a first serious pass, which is pretty much where I'd be starting from too. It's still one build per framework, and agents have likely seen far more Svelte and htmx than Cogent Core or MIQT, so small differences in code and style scores are probably noise. Where an app was missing something, I tried to work out whether that was the framework or the agent before blaming either one.

After the builds, I did fix a handful of bugs the agents left behind, mostly found when I ran the apps on Linux. Those fixes are recorded in the repo next to each app.

### How I measured

Every app exposes the same set of make targets, so one script can build, test and benchmark all of them the same way. It records:

- cold, incremental and no-op build times, and package sizes
- memory at idle and under load
- a scripted desktop run through the big table and card list: jumping to row 500,000, scrolling, sorting, searching and exporting, with frame gaps measured along the way (median of 5 runs)
- the same run in Chrome for the web builds, plus download size and page size
- test results, a cgo probe, and cross builds for Linux and Windows

The test environment:

    Apple M1 Max, 64 GB RAM
    macOS 26.6 (arm64)
    Go 1.27.3
    Chrome via Playwright, at 2x (Retina) scale
    Linux checks: Ubuntu 24.04 arm64 in Docker (X11 and Wayland)

I mostly work on Linux, so I also ran every app in an Ubuntu container to see what actually works there. That's a functional check only. Timings from a software-rendered container wouldn't mean much, so all the numbers in this post come from the Mac.

Everything is in the [repo](https://github.com/justinfx/go-gui-experiments): the spec, the apps, the harness, the raw metrics and the [full report](https://github.com/justinfx/go-gui-experiments/blob/main/results/REPORT.md).

### What a number has to mean

Raw milliseconds aren't very useful on their own. Is 280 ms vs 260 ms for a sort something anyone would ever notice? So I read every timing in terms of what a user would feel:

| Time | What it feels like |
|---|---|
| about 100 ms or less | instant |
| up to about 1 s | responsive |
| over 1 s | a noticeable wait |
| frame gaps over about 100 ms | visible stutter |

If two apps land in the same band, I treat them as tied.

### A few extra experiments

Some results raised a "but is that the framework's fault?" question, so I added a few variants:

- **-fitcap** (a fitted data layer): each app swaps the shared pager for a data layer that suits its toolkit, with the same limit of 2 requests in flight.
- **gotth-glaze:** the GoTTH app in a [glaze](https://github.com/crgimenes/glaze) desktop shell that builds with `CGO_ENABLED=0`.
- **miqt-gotth:** the GoTTH web UI inside [Qt WebEngine](https://doc.qt.io/qt-6/qtwebengine-index.html), through MIQT.

They aren't extra contestants. They answer specific questions where they come up.

I also made a few mistakes in the harness along the way. The worst was a scroll test whose pacing stretched out instead of recording late frames, which hid stutter until I fixed it. They're all [written up in the repo](https://github.com/justinfx/go-gui-experiments/blob/main/results/DEVIATIONS.md), along with every change to the method.

## The Whole Picture First

Before getting into each question, here's everything at once. Each factor was scored 1 to 5 by a separate Opus judge agent, which didn't build any of the apps, against the same [rubric](https://github.com/justinfx/go-gui-experiments/blob/main/SPEC.md#appendix-b-judge-rubric), using the code, screenshots and measurements. Style was judged blind from screenshots. I overrode a few performance scores after trying the apps by hand (Wails and the `-fitcap` builds of Wails and GoTTH), and every change and its reason is [in the repo](https://github.com/justinfx/go-gui-experiments/blob/main/results/judge2/changes.md). The scores are grouped by the question they belong to. Darker means stronger.

{{< svg "uploads/2026/11/heatmap-base.svg" >}}

And the same scores as one shape per stack, which makes it easier to see where each one leans:

{{< svg "uploads/2026/11/radar-base.svg" >}}

No stack is strong everywhere. These score the base apps as the agents built them, and a couple change once the data layer is fitted to the toolkit, most of all MIQT's large data score (2 to 4, covered below).

## Do I Have a Lot of Data?

This one matters for us, because a lot of our tools are basically different views onto large batches of results. Often that is a table, but not always. So the spec leans on it hard: a million rows in the table, a million items in a card view, and every row coming from a pager with 50 ms of latency.

The setup section showed the table. Here's the other view of the same million rows, the card list:

<div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(240px,1fr));gap:12px">
{{< zoom src="cards-dark-fyne.webp" alt="Fyne: the card view, a scrolling list of product cards with the detail form open." caption="Fyne" >}}
{{< zoom src="cards-dark-cogent.webp" alt="Cogent Core: the card view, a scrolling list of product cards with the detail form open." caption="Cogent Core" >}}
{{< zoom src="cards-dark-wails.webp" alt="Wails: the card view, a scrolling list of product cards with the detail form open." caption="Wails" >}}
{{< zoom src="cards-dark-gotth.webp" alt="GoTTH: the card view, a scrolling list of product cards with the detail form open." caption="GoTTH" >}}
{{< zoom src="cards-dark-miqt.webp" alt="MIQT: the card view, a scrolling list of product cards with the detail form open." caption="MIQT" >}}
</div>

{{< svg "uploads/2026/11/angle-data-heatrow.svg" >}}

### The desktop numbers

The scripted desktop run, median of 5 runs:

| | Fyne | Cogent | Wails | GoTTH | MIQT |
|---|---|---|---|---|---|
| Ready (ms) | 572 | 555 | 623 | 632 | **443** |
| Jump to row 500k (ms) | 146 | 113 | **90** | 232 | 666 |
| Table scroll, worst frame gap (ms) | 40 | **19** | 91 | 92 | 251 |
| Scroll steps showing blank rows (of 200) | **0** | 24 | 64 | 155 | 115 |
| Cards scroll, worst frame gap (ms) | 25 | **2.5** | 8 | 73 | 428 |
| Switch to cards (ms) | **104** | 112 | 194 | 969 | 928 |
| Sort (ms) | **254** | 262 | 280 | 365 | 622 |
| Search (ms) | **66** | **66** | 100 | 183 | 70 |
| Peak memory (MB) | 1412 | **513** | 824 | 730 | 702 |

A few terms. Ready is the time from launch until the window is up with its first page of data. A frame gap is how long the UI thread was too busy to run anything: every 16 ms the harness queues a no-op on it and records how late it ran. For the Go-drawn and Qt apps that goes through the toolkit's main thread, and for the WebView apps it's a JavaScript timer, so the two kinds of numbers aren't measured quite the same way. Blank rows are the placeholder rows a table shows while a page of data is still on its way from the pager. Bold marks the best value in each row, but it isn't a ranking: anything in the same band feels the same to a user.

Start-up, sort and search are responsive or instant everywhere. What a user would actually feel is scrolling and switching views. Fyne and Cogent scroll smoothly. Wails and GoTTH sit just under the stutter line. MIQT stutters visibly in both the table and the cards, and GoTTH and MIQT take close to a second to switch to the card view. GoTTH's blank rows and slow switch are partly by design: its spec asked for a windowed infinite scroll that loads around where you are, rather than a fully virtualized list.

{{< svg "uploads/2026/11/bands-desktop.svg" "The same desktop timings placed in the perception bands, on a log scale. Every action lands in instant or responsive for every app, with GoTTH and MIQT near the edge of a noticeable wait when switching to the card view. Scrolling is what separates them: only MIQT crosses the stutter line. Hover a mark for its value." >}}

Fyne peaks at about 1.4 GB, while the others land between 0.5 and 0.8 GB. On a normal workstation nobody would notice, but it would matter on a machine without much RAM.

### The obvious explanation was wrong

Is that stutter Qt, or is it how our app feeds rows to Qt? To separate the two, each app got a variant that swaps the shared pager for a data layer suited to its toolkit, still limited to 2 requests in flight like the shared core:

| | Fyne | Cogent | Wails | GoTTH | MIQT |
|---|---|---|---|---|---|
| Table scroll, worst gap: base (ms) | 40 | **19** | 91 | 92 | 251 |
| Table scroll, worst gap: -fitcap (ms) | 26 | 19 | 140 | 321 | **16** |
| Blank-row steps: base / -fitcap | 0 / 0 | 24 / 0 | 64 / 1 | 155 / 0 | 115 / 2 |

MIQT goes from visible stutter to the smoothest scroll of the lot, on the same Qt. So the stutter came from how our app fed rows to Qt, not from Qt itself.

The fitted layer does three things the base app didn't. It cancels requests for pages the view has already scrolled past, it prefetches ahead in the scroll direction based on how fast you're scrolling, and it tells Qt about new data with one batched update for the rows on screen, instead of an update per row. None of that is specific to MIQT. It would apply to any Qt model sitting on a slow backend. The code is in [`adapter_store_desktop.go`](https://github.com/justinfx/go-gui-experiments/blob/main/apps/miqt-fitcap/adapter_store_desktop.go).

With the fitted data layer, MIQT's large-data score goes from 2 to 4. It isn't a 5 because switching to the card view still takes about a second. Fyne and Cogent stay in the same band.

The WebView apps went the other way. Wails and GoTTH lose their blank rows, but their worst gaps grow, and GoTTH's gaps of over 300 ms keep repeating. On paper that's the worst in the table (more on how it felt below). Yet the same fitted builds are smooth in Chrome: 23 ms for Wails and 68 ms for GoTTH. Same HTML, different engine. To check that, I ran the same Wails web builds in Chrome and in a WebKit browser, three runs each. Chrome's worst gaps were 20 to 42 ms; WebKit's were 141 to 304 ms. So the engine showing your HTML on the desktop (WKWebView on macOS) clearly matters, though I haven't tracked down what WebKit is doing differently. These are all macOS numbers, and on Linux the engine would be WebKitGTK, which I didn't time.

{{< svg "uploads/2026/11/angle-data-miqt.svg" "Base MIQT and miqt-fitcap, with the large-data spoke highlighted. Same Qt app, only the data layer changed, and that one spoke moves from 2 to 4. Every other score is the same app." >}}

### Is that scroll test even realistic?

I had to ask myself this. The scroll test moves 25 rows every 16 ms, which is about 1,500 rows a second. That's a hard fling or a fast scrollbar drag through a million rows, not someone gently using the mouse wheel. It's a stress test, and I didn't want to call something a problem just because a machine could measure it.

So I manually tested Wails, which measured gaps of about 91 ms, and its fitted variant at about 140 ms. I have a mouse with a free-spinning wheel, which is about as aggressive as scrolling gets. Gentle scrolling was smooth in both. Flicking the wheel as hard as it would go still felt acceptable, with an occasional flicker. Dragging the scrollbar far showed blank rows while distant pages loaded, which seems normal given the data has to arrive from somewhere. The important part for me was that the controls themselves never lagged. Only the data caught up. And I honestly couldn't tell the two apart.

My take is that gaps in the 100 to 200 ms range are real and measurable, and some people could notice them, but to me they didn't stand out. Base MIQT, with gaps around 250 ms that keep repeating, really lagged when I spun the wheel fast or dragged the scrollbar, and the scrollbar itself fell behind, not just the data. But the fitted GoTTH variant, with repeated gaps around 320 ms on paper, only showed the most minor lag in my hands, and dragging was a lot more responsive than MIQT. What the gap numbers don't capture is whether the controls keep up. In the WebView apps they did, so a late frame read as data catching up. In base MIQT they didn't, and that's what felt broken. That fits how the two work. A browser engine scrolls and draws its scrollbar off the main thread, so a busy JavaScript thread shows up as late data. In Qt the UI thread also paints and handles input, so when it's busy, everything waits. Those hands-on checks are why Wails keeps a 5 for large data despite its blank-row steps. I didn't do one for base GoTTH.

Here's the same scripted scroll recorded on both MIQT builds: 200 steps of 25 rows from row 500,000, the exact run the numbers above came from. The fitted data layer finishes in about 3 seconds. The base app needs over 8 seconds for the same steps, because its UI thread keeps falling behind, and it shows blank rows most of the way.

{{< clip src="miqt-scroll.mp4" caption="The harness's fast scroll, about 1,500 rows a second, on the same Qt app with two data layers. Base on the left, fitted on the right, which stops when its run is done." >}}

### If you have a lot of data

What I saw: every one of these can handle a million rows, but not all of them do it out of the box. The Go-drawn toolkits, Fyne and Cogent, were smooth with the shared pager as is. Qt was smooth once the data layer worked the way Qt wants. The WebView stacks were fine to use by hand, but their feel depends on which browser engine is doing the drawing, and on desktop that's the system WebView, not Chrome. If your app lives in a big table, it's worth budgeting time for the data layer whichever one you pick.

## Do I Need cgo?

cgo has a build side and a runtime side. The build side, like needing a C compiler, Xcode tools or a Docker image to cross-compile, is something we can arrange with build systems and scripts. So for me the concern is the runtime side: what the binary needs when it runs. If I hand an app to someone on a different Linux box, what does it have to find on that machine before a window shows up?

There's also a second question hiding under "cgo": can the app use a Go core that itself uses cgo? We have a lot of C and C++ libraries at Wētā FX, and sometimes we have to wrap bindings to them, so the GUI approach has to be able to host that.

{{< svg "uploads/2026/11/angle-cgo-rows.svg" >}}

### Can it run a cgo core?

To check this, the shared core has a build tag that swaps one of its small Go functions, a CRC-32 checksum, for the same function written in C. Then I tried to build every app with that tag, for each of its targets. If it builds, that target can host a cgo core.

| | Fyne | Cogent | Wails | GoTTH | MIQT |
|---|---|---|---|---|---|
| Desktop | yes | yes | yes | yes | yes |
| Web | no | no | yes | yes | n/a |
| Web, with a Go server | yes | yes | n/a | n/a | n/a |

On the desktop, everything can. The web is where it splits. Fyne and Cogent run in the browser as WASM, and WASM can't call C. You can still get there by splitting the app: the UI stays WASM in the browser and talks to a separate Go server process that holds the core. That server is an ordinary Go program, so it can run on the user's own machine or on a shared server somewhere (in my tests it ran locally). Wails and GoTTH don't need that split, because on the web their Go code already runs as that kind of server process.

### What does the binary need at runtime?

This matters most on Linux. Here's what each Linux binary asks the dynamic linker for (from `objdump -p` on the x86-64 builds). That only shows what's linked at build time, not what a library loads later with `dlopen`, so treat it as a floor:

| App | Libraries the binary links |
|---|---|
| gotth-glaze | libc, libdl, libpthread (WebKitGTK loaded later, at runtime) |
| Cogent | libX11, libm, libgcc_s, libc |
| Fyne | libGL, libwayland-client, libm, libc |
| GoTTH (webview_go) | webkit2gtk-4.0, GTK 3, GLib, JavaScriptCore, libstdc++, libc |
| Wails | webkitgtk-6.0, GTK 4, libsoup 3, GLib, JavaScriptCore, libX11, libc |
| MIQT | Qt6Widgets, Qt6Gui, Qt6Core, libstdc++, libc |

Fyne and Cogent link only the basic X11, Wayland or GL libraries that any desktop install already has, and in the Ubuntu container they ran without any extra packages.

The WebView stacks need WebKitGTK, and the version matters. webview_go, which GoTTH uses as its desktop shell, links `webkit2gtk-4.0`, which Ubuntu 24.04 doesn't ship anymore, so a stock build of it simply doesn't start there. Wails v3 defaults to GTK 4 and `webkitgtk-6.0`, with a build tag to go back to GTK 3. Either way, you're now tracking which WebKitGTK your users' distro has.

MIQT needs Qt 6 at runtime, either from the system or shipped next to the app. In my case that's less of a worry than it sounds, since we use Qt a lot and it's always available for linking in our environments, even across different versions. But that's our setup, not a typical one. It's also the slowest to build from clean, about 2 minutes while the Qt bindings compile, though that's a one-time cost per machine or CI cache.

The gotth-glaze variant is the interesting one here. It builds with `CGO_ENABLED=0`, so the binary only links libc and friends, and it cross-compiles without Docker. But it still loads WebKitGTK when it opens a window, so it hasn't really escaped the runtime dependency. It just finds it later. And it can't link a cgo core, which rules it out for the tools where we do end up wrapping a C or C++ library. "No cgo" turned out not to mean "no native dependencies".

One more thing that applies to all of them: they link glibc dynamically, gotth-glaze included. A binary built on a newer distro can refuse to start on an older one. None of these approaches gets you out of that by default.

{{< svg "uploads/2026/11/angle-cgo-focus.svg" "Wails and Fyne, with the cgo core and runtime dependency spokes highlighted. They pull in opposite directions: Wails hosts a cgo core on both targets but needs WebKitGTK at runtime, while Fyne needs almost nothing at runtime but can't run a cgo core in the browser without a server." >}}

### If you need cgo

If the concern is a cgo core, Wails and GoTTH handle it on both desktop and web with no extra work, and Fyne and Cogent can get there on the web by moving the core into a separate Go server process. If the concern is what the binary needs at runtime, Fyne and Cogent ask the least of a Linux machine. The WebView stacks trade that for a WebKitGTK dependency whose version you have to track, and MIQT trades it for Qt. Nothing here gets away from glibc.

## Do I Need Web, Native, or Both?

This is where my colleague's "just make them all web apps" comment comes in. His point was that a browser should be all anyone needs: no local process to launch, and it doesn't matter whether someone is on macOS or Linux or how their environment is set up. But that runs into the other half of the opening problem, so the first thing to sort out is where the Go code runs, and as whom.

### Where Go runs, and as whom

| Route | Where the Go code runs | Acts on the host as |
|---|---|---|
| Native toolkit (Fyne, Cogent, MIQT) | the user's machine | the user |
| WebView shell (Wails, GoTTH desktop) | the user's machine | the user |
| Local server plus browser | the user's machine | the user |
| Shared server (Wails, GoTTH, or the Fyne and Cogent Go-server split) | the server | the server's account |
| WASM in the browser (Fyne, Cogent) | the browser sandbox | nothing on the host |

A web UI isn't the problem by itself. A web UI talking to a Go process that runs on the user's own machine still reads files as that user, launches that viewer in their environment, and respects their group permissions. It's the shared server that loses that, because then the Go code runs as whatever account the server runs as. And WASM in the browser can't touch the host at all.

To be fair, a server-side app can still act as the user. It's just a lot more work. You need real authentication, and then a service that is trusted to impersonate users for certain actions, with the level of permission that requires. That's a realistic option, but it's a much bigger commitment than a local process that simply is the user.

So for our tools that need to act as the user, "web" really means "a web UI over a local Go process". Wails and GoTTH both work that way on the desktop, inside a WebView window, and can also be served the normal way when an app doesn't need host access.

{{< svg "uploads/2026/11/angle-web-rows.svg" >}}

### What reaching both costs

Supporting both desktop and web didn't cost much extra code in any of them:

| | Fyne | Cogent | Wails | GoTTH |
|---|---|---|---|---|
| Shared lines | 2401 | 2561 | 3867 | 3392 |
| Desktop-only lines | 250 | 258 | 248 | 590 |
| Web-only lines | 130 | 100 | 197 | 231 |
| Target-specific share | 13.7% | 12.3% | **10.3%** | 19.5% |

Every approach landed somewhere between 10 and 20% of its code being specific to one target. Wails' real share is a bit higher, since some of its web-only `isWeb` checks sit unmarked in shared files and count as shared. GoTTH's desktop share is bigger because its desktop shell is where the native menus and dialogs live. MIQT has no web target, so it isn't in the table.

So the cost of reach isn't really the code. It's what the user gets on each side.

### What the user waits for on the web

{{< svg "uploads/2026/11/webload.svg" "What each web route makes the browser download before the app shows up, and how long until it's ready. Single runs in Chrome over a local connection. Hover a bar for its value." >}}

These are single runs in Chrome, so I only trust the big differences. Wails and GoTTH are ready in about a fifth of a second. Fyne and Cogent ship the whole Go app as WASM, so the browser downloads 7 to 12 MB and compiles it before anything shows up. That was over a local connection, too. Once loaded, Cogent was smooth, while Fyne stuttered while scrolling, and the Go server split didn't fix that, which points at rendering rather than data. GoTTH also went a little over the stutter line in this one Chrome run.

| | Fyne (WASM) | Cogent (WASM) | Wails | GoTTH |
|---|---|---|---|---|
| Scroll, worst frame gap (ms) | 126 | **15** | 21 | 152 |

<div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(240px,1fr));gap:12px">
{{< zoom src="desktop-dark-fyne.webp" alt="Fyne desktop build in its dark theme, table view with the detail form open." caption="Fyne on the desktop" >}}
{{< zoom src="web-dark-fyne.webp" alt="The same Fyne app compiled to WASM in Chrome, nearly identical, with a missing-glyph box in the ID header and 'unknown user' in the status bar." caption="The same Fyne app as WASM in Chrome" >}}
</div>

*Fyne draws its own widgets, so the WASM build looks almost identical to the desktop one. Two small differences: a missing-glyph box where the sort arrow should be in the ID header, and "unknown user", because code running in the browser can't see the host.*

I thought one of the Go-native toolkits might have a good web story by now, and it was better than I expected. They really do run in the browser, which is pretty impressive, but the first load is a real cost.

### What "native" gets you on the desktop

- **MIQT:** real Qt widgets, a native menu bar and Qt file dialogs.
- **Wails:** HTML controls, but with a native menu bar, native file dialogs and notifications.
- **GoTTH:** HTML controls with an in-page menu by default. A build tag adds a real macOS menu bar.
- **Fyne:** its own drawn controls and dialogs, with an in-window menu.
- **Cogent:** its own drawn controls, no native menu bar, its own file picker, and no notification API.

And on Linux, which is where I'd be running these (Ubuntu 24.04 container, X11 and Wayland, after the fixes):

| On Linux | Fyne | Cogent | Wails | GoTTH | MIQT |
|---|---|---|---|---|---|
| Menu bar | drawn in the window | drawn in the window | native GTK | in the page | native Qt |
| File dialogs | drawn in the window | drawn in the window | native GTK | zenity | Qt |
| Notifications | yes (D-Bus) | no API | yes (D-Bus) | yes | needs a tray watcher |
| Clipboard | yes | yes | yes | needs xclip or wl-clipboard | yes |
| Reveal a file | opens its folder | opens its folder | opens its folder | opens its folder | opens its folder |
| Extra runtime packages | none | none | WebKitGTK, GTK 3 | WebKitGTK 4.0, zenity, a clipboard tool | Qt 6 |

Wails is its GTK 3 build; the default GTK 4 build couldn't start in the container. MIQT's notifications need a tray watcher, which stock GNOME doesn't have. And GoTTH is just the web UI, so its column really describes its desktop shell (more below).

The flip side shows up in custom drawing. The spec asked for three custom table cells: a stock-level bar, a status badge and a star rating. The HTML stacks did them in about 100 lines of markup and Tailwind classes, on top of their component library. MIQT's Qt paint delegates took 300. Fyne and Cogent, which build everything from canvas shapes, took over 500 lines of Go each.

{{< zoom src="cells-strip.webp" alt="The same product card in GoTTH, Wails, MIQT, Cogent Core and Fyne, each with a status badge, a five-star rating and a stock bar, labeled with the lines of code each took for its custom cells: 90, 100, 300, 550 and 581." caption="The same card in each app, with the status badge, star rating and stock bar. Next to each is how many lines its custom cells took." >}}

On looks, I had the screenshots judged blind. Wails and GoTTH share the shadcn look and came out on top. That isn't quite like-for-like: the spec held every app to its default theme, and for those two the default included a component library. Fyne and MIQT were clean but plain. Cogent came last, with clipped columns and unboxed form fields in the table view.

{{< svg "uploads/2026/11/angle-web-focus.svg" "Cogent and MIQT, with the web reach and native widgets spokes highlighted. They pull apart: Cogent reaches the most targets from one codebase and feels the least native, while MIQT is the most native and has no web build at all." >}}

### Other desktop shells for a web app

If a web UI over a local Go process is the shape you want, the desktop shell becomes a choice of its own. So I put the same GoTTH web app in two more shells: glaze, a WebView library that builds without cgo, and MIQT's Qt WebEngine view.

| | GoTTH (webview_go) | gotth-glaze | miqt-gotth |
|---|---|---|---|
| macOS cold build (s) | 9.4 | 8.1 | 133 |
| macOS bundle (MB) | 11.1 | 9.5 | 335 |
| macOS scroll, worst gap (ms) | 92 | 83 | 280 |
| macOS switch to cards (ms) | 969 | 970 | 1,859 |
| Cross builds | Linux, Windows | Linux, Windows, no Docker | none |
| Linux menu bar | in the page | in the page | native Qt |
| Linux file dialogs | zenity | native GTK | Qt |
| Linux notifications | yes | no | needs a tray watcher |
| Linux clipboard | needs xclip or wl-clipboard | through the page | yes |
| Linux extra packages | WebKitGTK 4.0, zenity, a clipboard tool | WebKitGTK | Qt 6 with WebEngine |

gotth-glaze feels the same as GoTTH and cross-compiles without Docker. On a Mac the visible difference is the menu: glaze puts it in the macOS menu bar, while GoTTH draws it inside the page. On Linux it swaps zenity for real GTK file dialogs and needs fewer packages, but glaze is new and has no Linux notifications yet. The webview_go shell only has them because the app pulled in a small library for it, so that's the kind of gap you'd fill yourself.

miqt-gotth gets native Qt menus, dialogs and clipboard around the web UI, but at 335 MB, mostly Qt WebEngine, with visible stutter and an almost 2 second card switch. Wrapping a web UI in Qt didn't get the best of both.

{{< svg "uploads/2026/11/heatmap-shells.svg" "The same GoTTH web app in three desktop shells, scored on the same factors." >}}

### If you need web, native, or both

If your app has to act as the user, the line that matters isn't web versus native. It's whether the Go code runs on the user's machine. Native toolkits and WebView shells both do. The trade-off is that running on the user's machine means a local process again, which is exactly what a pure web app avoids. So the browser-only route works for the tools that don't need host access. The rest either need something installed, or a server that is trusted to impersonate users, with all the authentication and permissions work that brings. Beyond that, it comes down to experience: Wails and GoTTH load fast on the web and look the most polished, Fyne and Cogent carry a big WASM download, and MIQT gives the most native desktop at the price of having no web build at all.

## Wrapping Up

There wasn't a single standout result that made one of these my choice for everything. Each one had different strengths depending on the angle. What surprised me was how many small details play into whether one of these turns out well. If you're adding a view layer onto existing business logic, it would be easy to get poor performance out of MIQT at first and blame Qt, when it was really how the data was fed to it. Or to find that the same HTML behaves differently depending on which WebView engine is showing it.

As for my starting assumptions, the Go-native toolkits had a better web story than I expected, even with the big first load. Fyne did a bit better than I thought it would, so it must have come a long way since I last looked at it. I expected Wails to do well, and it did, but I was surprised how competitive the GoTTH stack was.

As for my colleague's point, I can see how there's an easier path to a fully served web app if it's already a web UI over a local Go process, like Wails or GoTTH, and the other way around. But I don't think you can say every app can always be a remotely served web app. My stance is still the best tool for the job, and knowing the strengths of the different options is ideal for that.

For my own needs right now, Wails seems like the strongest pull, assuming WebKitGTK is something we can expect to find on the host. I haven't timed it on WebKitGTK, and the engine turned out to be the biggest variable I found. But it came out with some of the strongest results, and the performance was still good.

And given how much I dislike writing web frontends, having a coding agent do that part frees me up to have more options. Coding agents are pretty good at web frontends anyway, so I don't have to be.

The code, the harness and all the raw results are on [GitHub](https://github.com/justinfx/go-gui-experiments).


---

**Update, November 14, 2026:** Readers have suggested other options since this was published. I'm putting each one through the same spec and harness, and posting the results as [addenda on the project wiki](https://github.com/justinfx/go-gui-experiments/wiki/Addenda).
