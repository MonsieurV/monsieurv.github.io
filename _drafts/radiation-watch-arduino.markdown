---
layout: post
author: yoan
title: "A Geiger counter for your house - Part 1"
categories: [Embedded systems]
tags: [Embedded systems, data logging, Arduino, Geiger counter]
brief: "Play with an Arduino and the Radiation Watch Pocket Geiger to monitor radiation."
draft: true
lovehatefeedback: true
---

Some month ago a [client of mime][effi_synchrone] have offered me a Geiger counter kit from [the Radiation Watch project][]. The last few weeks I was able to take the the time to play with.

Radiation Watch is a scientific and citizen initiative born after the Fukushima Daiishi disaster. The project have been founded [on Kickstarter][rw_ks] by N people TODO date. It aims to provide a cheap and accessible radiation sensor for monitoring gamma radiation.

In the case you don't remember well - or never knew - here how we usually classify radiations: we distinguish between Alpha, Beta and Gamma rays. What is especially remarkable is that the three have longueur d'onde croissante, which induce their capacity to traverse the universe. Ainsi a simple sheet of paper or layer of clooth suffice to block alpha radiations; as for beta rays you'll need to stay behind ?? to be protected from a source. Finally the gamma rays are only stopped by ???: they're the one to which we are naturally exposed. If you don't play with direct sources like [Marie Curie][] or don't [go around][] in a highly contaminated site you won't be exposed to significant level of alpha or beta rays.

Rayonnement alpha & beta: exposition naturelle ? Soleil ?

Schema of radiation, longueur d'onde, and isolation.

Relation between radioactivity and radiation dose. The Sievert unit.

Ok, I really don't know much about radioactivity so now better // let's begin to speak code.

Picture of Pocket Geiger connected to the Arduino

http://thegovlab.org/wiki/Radiation-Watch.org

The Radiation Watch is a no-brainer to interface with your arduino: 4 wires to connect directly and you're done. Near all the complexity is embedded on the Pocket Geiger card: and you don't have to worry about it.

Provide a better firmware. IRQ, cleaned code, clearer interface and reduced memory footprint.

SD card data logging.

All is good, and for one purpose: measure the background radiation level. We'll see here what it can gives you.

{% include figure.html img="/assets/2015-12-04-radiation-watch-arduino/colombes_2015_12_01_radiation.svg" url="https://plot.ly/~tournadey/15/colombes-1st-december-2015-gamma-radiation/" alt="Plot of the radiation level at Colombes the 1st December 2015" linkTitle="Click to see the data on Plotly" %}

Histogram. Standard deviation.

Surprinsgly I was able to take measurements being in a train. I expected the vibration to fuck up, but it wasn't the case. The accelerometer wasn't triggering the noise detection pin and the data seems plausible.

{% include figure.html img="/assets/2015-12-04-radiation-watch-arduino/bordeaux_agen_train_2015_12_02_radiation.svg" url="https://plot.ly/~tournadey/30/bordeaux-to-agen-on-train-2nd-december-2015-gamma-radiation/" alt="Plot of the radiation level on the train from Bordeaux to Agen the 2nd December 2015" linkTitle="Click to see the data on Plotly" %}

Finally Go to Golfech and make some background measurements for 2 hours.

Since this device seems good for background monitoring, I'll connect it to my Raspberry Pi and plot the data online in real-time. Stay tuned.

http://www.playspoon.com/wiki/index.php/GeigerCounter
http://www.radiation-watch.co.uk/aboutus
http://www.radiation-watch.co.uk/faqs

[effi_synchrone]: http://www.effi-synchrone.com
[rw_ks]: https://www.kickstarter.com/projects/1517658569/smart-radiation-detector/description
[safecast_bgeigie_nano]: http://shop.kithub.cc/products/safecast-bgeigie-nano?variant=10879588932
