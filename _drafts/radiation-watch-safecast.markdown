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


Get last live data from Safecast API and plot it with Plotly.

<figure>
  <div id="plot-safecast-soubeyrac"></div>
  <figcaption>Caption</figcaption>
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
        xaxis: {
        title: 'Timestamp'
        },
        yaxis: {
        title: 'Radiation dose (uSv/h)'
        },
        margin: {
          t: 10
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

Another project followed the Fukushima crisis: the bGeigie by Safecast provides a complete and full-pledged radiation sensor. Compared to the Radiation Watch Pocket Geiger, [it is not cheap][safecast_bgeigie_nano], but way more complete. Mobile radiation measurement. It comes as a kit to build and allows to monitor all Alpha, Beta and Gamma radiations. It can be considered as a whole system: provides an API. Map. Community.

Rpi: Connect this to the Safecast API for stationary measurements. TODO Send message to Safecast

Python lib
https://github.com/MonsieurV/SafecastPy

https://drive.google.com/a/effi-synchrone.com/file/d/0B7IJHJBUg61CZjk0YTQ1ZDYtOWQ0My00MzRlLWJmZTQtYTAyMTUyMzM3ZDBi/view

About data
http://blog.safecast.org/faq/data/
Useful data - best practices
http://blog.safecast.org/2014/01/useful-data/
About sensor
http://blog.safecast.org/faq/radiation/

Post in https://groups.google.com/forum/#!topic/safecast-devices/nxA8XxFzD0E

https://kithub.cc/safecast/
First:
https://www.kickstarter.com/projects/1038658656/rdtnorg-radiation-detection-hardware-network-in-ja/description
Then:
https://www.kickstarter.com/projects/seanbonner/safecast-x-kickstarter-geiger-counter/description

http://safecast.org/tilemap/?y=46.9&x=2.77&z=7
https://books.google.fr/books?id=2WCAAAAAQBAJ&pg=PA114&lpg=PA114&dq=radiation+watch+kickstarter&source=bl&ots=ysQnoMqI83&sig=ZRcUa9MnRsWlr69-_17mEvOISik&hl=fr&sa=X&ved=0ahUKEwiTp7XkibHJAhWBvxoKHaZNAssQ6AEIRzAE#v=onepage&q=radiation%20watch%20kickstarter&f=false
http://realtime.safecast.org/

[safecast_bgeigie_nano]: http://shop.kithub.cc/products/safecast-bgeigie-nano?variant=10879588932
