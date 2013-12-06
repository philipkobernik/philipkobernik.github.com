---
layout: post
title: "Plotting comparative time series with JqPlot"
date: 2013-11-25 15:26:48 -0800
comments: true
categories: [Javascript, JqPlot, Analytics]
---

## JqPlot takes some effort

I was compelled to use JqPlot for a work project.

My first advice would be this: Run. JqPlot is tough to learn and not fun to play with. You will find yourself eternally knee deep in a hash of configuration options. It is canvas based and does not render nicely on retina displays.

If you are still reading, so be it. May this blog post spare you some pain.

## Stacking two disparate time ranges

Your starting point looks like [this](http://jsfiddle.net/ptk921/R92tG/1/). You have two time-series that are being rendered with the DateAxisRenderer.

The core of the solution is to specify two x-axes so that the
time series can be 'stacked' on on top the other for
comparison.

Two changes to the configuration must be made:
 * change the axes configuration to define a 2nd x-axis called x2axis
 * change the series configuration to specify that one of the data
   series should use the new 'x2axis' as its xaxis.


``` javascript
var plot1 = $.jqplot('chart1', [line1,line2], { 
  title: 'XYZ, Inc.', 
  series: [{ 
    label: 'primary series', 
    neighborThreshold: -1,
    xaxis: 'xaxis'
  }, {
    label: 'comparative series',
    xaxis: 'x2axis'
  }], 
  axes: { 
    xaxis: { 
      renderer:$.jqplot.DateAxisRenderer,
      tickRenderer: $.jqplot.CanvasAxisTickRenderer,
      tickOptions: {
        angle: -30
      } 
    },
    x2axis: {
      renderer:$.jqplot.DateAxisRenderer,
      tickRenderer: $.jqplot.CanvasAxisTickRenderer,
      tickOptions: {
        angle: -30
      } 
    },
    yaxis: {  
      renderer: $.jqplot.LogAxisRenderer,
      tickOptions:{ prefix: '$' } 
    } 
  }
});

```

This gives us what we're looking for, [two stacked time series](http://jsfiddle.net/ptk921/R92tG/4/).


## More ~~problems~~ opportunities

The above case is rather ideal, and actually makes JqPlot look pretty
good. What happens when your time series have different lengths?

If your time series have different lengths, the two x-axes will have
different ranges, and tick intervals and vertical gridlines wil not
match up. In short, the stacked effect will be destroyed.

In [this example](http://jsfiddle.net/R92tG/5/), I have destroyed the
stacked effect by adding two data points to the data series 'line1'--
one point on the front, and one on the end.

There are many reasons why your data series have unequal lengths like
this. In my case, comparing months of data by day resulted in data series
with different lengths due to months having uneven numbers of days. Silly months.

In any case, the best solution for his kind of problem is to explicitly
define the x-axes' chart range parameters via the JqPlot 'min' and 'max'
options.

``` javascript
axes: { 
  xaxis: { 
    renderer:$.jqplot.DateAxisRenderer,
    tickRenderer: $.jqplot.CanvasAxisTickRenderer,
    tickOptions: {
      angle: -30
    },
    min: "6/22/2009 09:00",
    max: "6/22/2009 22:00"
  },
  x2axis: {
    renderer:$.jqplot.DateAxisRenderer,
    tickRenderer: $.jqplot.CanvasAxisTickRenderer,
    tickOptions: {
      angle: -30
    },
    min: "6/23/2009 09:00",
    max: "6/23/2009 22:00"
  },
  yaxis: {  
    renderer: $.jqplot.LogAxisRenderer,
    tickOptions:{ prefix: '$' } 
  } 
},
```

[Example showing min/max solution here](http://jsfiddle.net/R92tG/6/).

## Use tickInterval

One handy discovery in my JqPlot adventure was the 'tickInterval'
option. With the x-axes' min and max options set, one can utilize
    tickInterval: '1 (day|week|month)'
to quickly and easily modify the rendering of tick marks. Thanks JqPlot.


## Did this help?

Feel free to upvote my [stackoverflow answer](http://stackoverflow.com/a/20227772/1128839) if this post helped you out.
