---
layout: post
author: yoan
title: "A Geiger counter for your house - Part 2"
categories: [Embedded systems]
tags: [Embedded systems, data logging, Raspberry Pi, Geiger counter]
brief: "Keep an eye on those sneaky gamma rays using your Raspberry Pi."
draft: true
---

Follow-up of my article about the Pocket Geiger. So far we were data logging the radiation level offline. I was promising a counter for your house: we need to do better.

Since this device seems good for background monitoring, I'll connect it to my Raspberry Pi and plot the data online in real-time. Stay tuned.

Another project followed the Fukushima crisis: the bGeigie by Safecast provides a complete and full-pledged radiation sensor. Compared to the Radiation Watch Pocket geiger, [it is not cheap][safecast_bgeigie_nano], but way more complete. Mobile radiation measurement. It comes as a kit to build and allows to monitor all Alpha, Beta and Gamma radiations. It can be considered as a whole system: provides an API. Map. Community.

Rpi: Connect this to the Safecast API for stationary measurements. TODO Send message to Safecast

RPi: propose a built-case for 200 €. Monitore (to Safecast? to a backend of mime? to a custom endpoint?) as soon as you connect it to internet (Ethernet with DHCP).


http://safecast.org/tilemap/?y=46.9&x=2.77&z=7
https://books.google.fr/books?id=2WCAAAAAQBAJ&pg=PA114&lpg=PA114&dq=radiation+watch+kickstarter&source=bl&ots=ysQnoMqI83&sig=ZRcUa9MnRsWlr69-_17mEvOISik&hl=fr&sa=X&ved=0ahUKEwiTp7XkibHJAhWBvxoKHaZNAssQ6AEIRzAE#v=onepage&q=radiation%20watch%20kickstarter&f=false
http://realtime.safecast.org/

[safecast_bgeigie_nano]: http://shop.kithub.cc/products/safecast-bgeigie-nano?variant=10879588932
