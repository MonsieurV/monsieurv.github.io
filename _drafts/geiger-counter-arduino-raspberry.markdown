---
layout: post
author: yoan
title: "Geiger counter for your house"
categories: [Embedded systems]
tags: [Embedded systems, data logging, Arduino, Geiger counter]
brief: "Play with the Radiation Watch Pocket Geiger and monitor the gamma ray in your house."
draft: true
---

A [client of mime][effi_synchrone] have offered me a Geiger counter kit to play with.

Radiation Watch is a scientific and citizen initiative born after the Fukushima Daiishi disaster. The project have been founded [on Kickstarter][rw_ks] TODO date. It aims to provide a cheap and accessible radiation sensor for monitoring Gamma rays.

http://thegovlab.org/wiki/Radiation-Watch.org
{% include figure.html img="/img/2015-11-01-findpeaks-in-python/peakutils_indexes.png" caption="The PeakUtils indexes function is easy to use and allows to filter on an height threshold and on a minimum distance between peaks." alt="Plot of results from PeakUtils indexes" %}

Another project followed the Fukushima crisis: the bGeigie by Safecast provides a complete and full-pledged radiation sensor. Compared to the Radiation Watch Pocket geiger, [it is not cheap][safecast_bgeigie_nano], but way more complete. Mobile radiation measurement. It comes as a kit to build and allows to monitor all Alpha, Beta and Gamma radiations. It can be considered as a whole system: provides an API. Map. Community.

Provide a better firmware. Connect this to the Safecast API for stationary measurements. TODO Send message to Safecast

http://www.playspoon.com/wiki/index.php/GeigerCounter
http://www.radiation-watch.co.uk/aboutus
http://www.radiation-watch.co.uk/faqs
http://safecast.org/tilemap/?y=46.9&x=2.77&z=7
https://books.google.fr/books?id=2WCAAAAAQBAJ&pg=PA114&lpg=PA114&dq=radiation+watch+kickstarter&source=bl&ots=ysQnoMqI83&sig=ZRcUa9MnRsWlr69-_17mEvOISik&hl=fr&sa=X&ved=0ahUKEwiTp7XkibHJAhWBvxoKHaZNAssQ6AEIRzAE#v=onepage&q=radiation%20watch%20kickstarter&f=false
http://realtime.safecast.org/

[effi_synchrone]: http://www.effi-synchrone.com
[rw_ks]: https://www.kickstarter.com/projects/1517658569/smart-radiation-detector/description
[safecast_bgeigie_nano]: http://shop.kithub.cc/products/safecast-bgeigie-nano?variant=10879588932
