---
layout: post
author: yoan
title: "A Geiger counter for your house - Part 3"
categories: [Embedded systems]
tags: [Embedded systems, data logging, radiation, Geiger counter, open-data, Safecast]
brief: "Featuring the Safecast open-data initiative."
lovehatefeedback: true
draft: true
---

In [the previous part](/2016/03/04/radiation-watch-raspberry/) we show how to publish and share our radiation measurements through internet services. But what is their value if we don't have an appropriate way to navigate, consult, visualize and centralize these measurements with those of others? How to give the measurements a meaning and a purpose?

To do better we should ensure the data will last beyond our enthusiastic-initiative and can be retrieved and exploited by others.

#### Open data with Safecast

We're fortunate because this is exactly what the Safecast project aims to provide: an open platform for everyone to publish their environmental data, with great [navigation and visualization tools](http://safecast.org/tilemap/), a [real-time portal](http://realtime.safecast.org/) and last but not least a hacker [data oriented mindset](http://blog.safecast.org/faq/).

The [Safecast initiative](http://blog.safecast.org/about/) also followed the Fukushima crisis, in an urge to collect and publish accurate and trustworthy open radiation data, openly accessible to citizens of the world{%include footnote_ref.html number="1" %}. The volunteer-driven organization has been known to provides efficient technical means, like the bGeigie Nano device, a full-pledged radiation sensor.

Compared to the Radiation Watch Pocket Geiger, the bGeigie [it is not cheap](http://shop.kithub.cc/products/safecast-bgeigie-nano){%include footnote_ref.html number="2" %}, but way more complete and accurate. With its GPS chip and real-time clock it allows effective mobile radiation measurements. Its [pancake](http://www.lndinc.com/products/17/) can monitor all Alpha, Beta and Gamma radiations. The device has been successfully used to collect millions of data-points.

Past the hardware, maybe the most interesting part is the whole system conceived and maintained by the Safecast community to store, share and consult the data. With the [Safecast API](https://api.safecast.org/en-US/home) measurements can be made available and controlled by citizens, researchers and journalists.

#### Python wrapping the API

To made things really easier I've developed a Python wrapper for the Safecast API: here comes [SafecastPy](https://github.com/MonsieurV/SafecastPy). The implementation was done following the Twitter API wrapper [Twython library](https://github.com/ryanmcgrath/twython/) as a model.

To make it works, that's the usual Python pip-ABC:

```pip install SafecastPy
```

Then do whatever you want{%include footnote_ref.html number="3" %}. Only do please be [conscientious](http://blog.safecast.org/2014/01/useful-data/) about the data you sent to the Safecast API, as this is a voluntarily maintained database. To test things out you can use the [development instance](http://dev.safecast.org/), which do not fear messy data.

As Python is a very convenient language to manipulate near any data format you'll have no difficulty broadcasting you're own sensor data to the Safecast API. This way your data will gain in power by contributing to a worldwide and open database.

You can also use the library to navigate through the Safecast open-data and use it on your own project.

#### Radiation Watch featuring Safecast

Coming back to our Pocket Geiger playground, we can now publish its measurements to the Safecast API. That will be our way to create a bridge between these two great initiatives than are Radiation Watch and Safecast.

With the [PiPocketGeiger](https://github.com/MonsieurV/PiPocketGeiger) this is a no-brainer

To test things out I myself maintain one of my Raspberry Pi logging my Pocket Geiger readings to Safecast:

<figure>
  <div id="plot-safecast-soubeyrac"></div>
  <figcaption>These measurements are published to the Safecast API by my Raspberry Pi, and grabbed back and plotted for you in this browser with plotly.js. Inspect the page to see the javascript sources.</figcaption>
</figure>

<script type="text/javascript" src="https://cdn.plot.ly/plotly-1.5.0.min.js"></script>
<script src="https://code.jquery.com/jquery-2.2.2.min.js" integrity="sha256-36cp2Co+/62rEAAYHLmRCPIych47CvdM+uTBJwSzWjI=" crossorigin="anonymous"></script>
<script>
  var data = [ { x: [], y: [], type: 'scatter' } ];
  function doPlot() {
    // TODO To make it responsive at window resizing
    // https://plot.ly/javascript/responsive-fluid-layout/
    Plotly.plot(
      document.getElementById('plot-safecast-soubeyrac'),
      data,
      {
        title: 'Yoan\'s home radiation',
        xaxis: {
        title: 'Timestamp'
        },
        yaxis: {
        title: 'Radiation dose (uSv/h)'
        }
      }
    );
  }
  // Download 100 data points (25*4).
  var PAGES = 4;
  var promises = [];
  var results = [];
  for(var i = 1; i <= PAGES; i++) {
    promises.push(
      new Promise(function(i, resolve, reject) {
        jQuery.get('https://api.safecast.org/measurements.json?order=captured_at+desc&user_id=992&page=' + i, function(i, response) {
          results[i] = response;
          resolve();
        }.bind(null, i));
      }.bind(null, i))
    );
  }
  Promise.all(promises).then(function () {
    for(var i = PAGES; i >= 1; i--) {
      results[i].reverse().forEach(function(dataPoint) {
        data[0].x.push(dataPoint.captured_at.replace('T', ' ').replace('Z', '')),
        data[0].y.push(dataPoint.value)
      });
    }
    doPlot();
  });
</script>

You can retrieve them directly on the Safecast API [website]((https://api.safecast.org/en-US/users/992/measurements?order=captured_at+desc)).

I hope you've enjoyed this series. Don't miss the chance to [tell me](mailto:yoan@ytotech.com)! If you have great projects in the same vein I'll be very happy to hear them from you. I myself have ideas following on from that series. Maybe you'll have funnier ones?

<br>

##### Notes

{% include footnote.html number="1" note="See complete history [here](http://blog.safecast.org/history/)." %}

{% include footnote.html number="2" note="Nor it is ready to use, as it comes as a kit whose requires [some work](https://github.com/Safecast/bGeigieNanoKit/wiki/NANO-MANUAL) to assemble." %}

{% include footnote.html number="3" note="For the usage please refer to the [GitHub README](https://github.com/MonsieurV/SafecastPy#basic-usage)." %}

{% include footnote.html number="4" note="I'm already sorry for the data logging discontinuity, caused by my internet connection very too frequent outages. I really do need to relocate." %}
