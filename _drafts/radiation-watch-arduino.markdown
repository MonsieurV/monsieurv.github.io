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

Some month ago a [client of mime][effi_synchrone] offered me a Geiger counter kit from [the Radiation Watch project][rw]. The last few weeks I was able to take the time to play with it.

{% include figure.html img="/assets/2015-12-04-radiation-watch-arduino/pocket_geiger.png" alt="Pocket Geiger illustration" %}

Radiation Watch is a scientific and citizen initiative born after the Fukushima Daiishi disaster and which has been funded through [Kickstarter][rw_ks] in July 2011. It aims to provide a cheap radiation detector that [anyone can use][rw_userreports], even [boars][rw_boars].

Initially developed to be connected to an iPhone, Radation Watch now provides a embedded version of the PocketGeiger. Whatever version is used, the board uses a [X100-7 PIN photodiode][X100_datasheet] from FirstSensor for gamma-ray detection.

In you don't remember well - or never knew - we classify radiations under three hats: alpha, beta and gamma rays. Each has its own  longeur d'onde and puissance. What is especially remarkable is that the three have longueur d'onde croissante, which induce their capacity to traverse the matter. Ainsi a simple sheet of paper or layer of clooth suffice to block alpha radiations; as for beta rays you'll need to stay behind ?? to be protected from a source. Finally the gamma rays are only stopped by ???: they're the one to which we are naturally exposed.
Ionizing capacity Vs. capacity to traverse the matter.

So if you don't [play with radioactive sources][marie_curie_death] or [ingest contaminated substances][radium_girls] you hopefully won't be exposed to significant level of alpha and beta rays, except maybe from [the radon in your house][radon_house].

{% include figure.html img="/assets/2015-12-04-radiation-watch-arduino/radiation_penetration.svg" url="https://en.wikipedia.org/wiki/Radiation" alt="Alpha, beta and gamma radiation penetration" caption="The ability to penetrate matter and ionize cells depends on the type of radiation (image from Wikipedia)." %}

Relation between radioactivity and radiation dose. The Sievert unit.
https://upload.wikimedia.org/wikipedia/commons/2/20/Radiation_Dose_Chart_by_Xkcd.png

PocketGeiger has. What?

Ok, I really don't know much about radioactivity so now better // let's begin to speak code.

{% include figure.html img="/assets/2015-12-04-radiation-watch-arduino/setup_photo.jpg" url="/assets/2015-12-04-radiation-watch-arduino/setup_photo.jpg" alt="Pocket Geiger connected to the Arduino" caption="Assembling the thing is not a big deal: weld four wires on the Pocket Geiger card and connect them to your Arduino." %}

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
[rw]: http://www.radiation-watch.org/
[rw_ks]: https://www.kickstarter.com/projects/1517658569/smart-radiation-detector/description
[rw_userreports]: http://www.radiation-watch.org/p/usersreports.html
[rw_boars]: http://www.radiation-watch.org/2013/08/asahi-shinbun-featured-pocketgeiger.html
[X100_datasheet]: http://www.mouser.com/ds/2/313/X100-7_SMD_501401-586455.pdf
[marie_curie_death]: https://en.wikipedia.org/wiki/Marie_Curie#Death
[radium_girls]: https://en.wikipedia.org/wiki/Radium_Girls
[radon_house]: https://en.wikipedia.org/wiki/Radon#Accumulation_in_buildings
[safecast_bgeigie_nano]: http://shop.kithub.cc/products/safecast-bgeigie-nano?variant=10879588932
