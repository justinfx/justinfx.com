---
title: Python API for XML/SWF Charts (Flash Graphing)
author: Justin Israel
type: post
date: 2010-01-15
slug: python-api-for-xmlswf-charts-flash-graphing
url: /2010/01/15/python-api-for-xmlswf-charts-flash-graphing/
meta_keywords:
  - graph, flash, swfchart, python, xml, xml/swf charts, graphing api
categories:
  - Code
tags:
  - flash
  - graph
  - python
  - swfchart
  - xml/swf charts
---
I had a project where I was designing a statistics reporting site, to track production stats. I wanted to have really nice graphs that pulled from the database and were somewhat live and interactive. XML/SWF Charts is this awesome flash-based app that lets you embed graphs in your pages which receive their data from XML. There are tons of graph types and ways to customize the look.

[<img class="alignnone size-full wp-image-59" title="graphs_example" src="/uploads/2010/01/graphs_example.jpg" alt="" width="754" height="112" />](http://www.maani.us/xml_charts/index.php)

<http://www.maani.us/xml_charts/index.php>

So, the thing is&#8230; I wanted to use Django for the site. I decided to write a python module that wraps around the API for SWF charts, so that the XML could easily be generated for me after just setting all my parameters. Thought I might post this here for any python fans wanting nice looking graphs in their site.

Really, this doesn&#8217;t just apply to django. You could just call out from anything to python code that will generate your XML for you.

Example:

{{< highlight python >}}
chart = SwfChart()

# the data
chart.addRow( 'Person A', 1, 10, 5.5 )
chart.addRow( 'Person B', 6, 4.45, 8 )
chart.addColumnLabel( 'Day1', 'Day2', 'Day3' )

# Extra graph settings
chart.addFilter('shadow', 'low', distance=2, angle=45, alpha=35, blurX=5, blurY=5)
chart.addFilter('shadow', 'high', distance=3, angle=45, alpha=35, blurX=10, blurY=10)
chart.addFilter('bevel', 'bevel1', strength=10, quality=3, distance=2)
chart.setChartBorder(top=1, bottom=2, left=0, right=0, color='000000')
chart.setChartLabel(color='000000', alpha=80, size=8, position='outside', hide_zero=True)
chart.setChartTransition(type='scale', delay=.5, duration=.5, order='series')
chart.setChartRect(shadow='high')
chart.setLegend(size=12, alpha=90, fill_alpha=30)
chart.setAxisTicks(category_ticks=True, value_ticks=True, minor_count=3)
chart.setContextMenu(about=False)

# if you have the licensed version
chart.setLicense('license string')
{{< /highlight >}}

Now at this point, the object can be treated like a string to get the xml, or just call getXML() :

{{< highlight python >}}
print chart

print chart.getXML()   # same thing

xml = str(chart)   # or assign the XML string somewhere
{{< /highlight >}}

You can download the swfcharts.py module and use it freely. If you find it helpful, post a comment or shoot me an email.

#### Download [swfcharts.py](/uploads/2010/01/swfcharts.py_.zip)

Pydoc located here for convenience:<span style="color: #ffcc00;"><br /> <a href='/uploads/2010/01/swfcharts.html'>swfcharts python documentation</a></span>
