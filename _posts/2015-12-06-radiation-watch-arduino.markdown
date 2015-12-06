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

{% include figure.html img="/assets/2015-12-06-radiation-watch-arduino/pocket_geiger.png" alt="Pocket Geiger illustration" caption="In the little white box resides the Pocket Geiger board, with its photodiode sensor." %}

Radiation Watch is a scientific and citizen initiative born after the Fukushima Daiishi disaster and which has been funded through [Kickstarter][rw_ks] in July 2011. It aims to provide a cheap radiation detector that [anyone can use][rw_userreports], even [boars][rw_boars].

Initially developed to be connected to an iPhone, Radation Watch now provides a embedded version of the PocketGeiger. Whatever version is used, the board uses a [X100-7 PIN photodiode][X100_datasheet] from FirstSensor for [gamma-ray detection][rw_uk_faqs].

In you don't remember well we usually classify radiations under three hats: alpha, beta and gamma rays. Alpha and beta radiation are charged particles, whereas gamma rays are photons of electromagnetic energy, with no charge and mass. They are all three considered ionizing radiation - which means they can [alter][alter_matter] the matter they penetrate -, but have very different penetrating and ionizing abilities due to their respective mass, size and nature.

Alpha and beta radiation can be stopped by an aluminum foil: if you don't [play with radioactive sources][marie_curie_death] or [ingest contaminated substances][radium_girls] you hopefully won't be exposed to significant level of alpha and beta rays, except maybe from [the radon in your house][radon_house]. In contract the very high frequency electromagnetic nature of gamma rays gives them a super high ability to traverse matter: imagine your Wifi on steroids, with a frequency above of [10 exahertz][spectrum] and an [energy][electronvolt] more than 100,000,000 greater. Yes, it can cooks your DNA like your microwave boils your Chinese noodles.

{% include figure.html img="/assets/2015-12-06-radiation-watch-arduino/radiation_penetration.svg" url="https://en.wikipedia.org/wiki/Radiation" alt="Alpha, beta and gamma radiation penetration" caption="The ability to penetrate matter and ionize cells depends on the type of radiation (image from Wikipedia)." %}

When you are considering [background radiation][bg_rad] monitoring, detecting only gamma rays gives you a sensible indication of your exposition to radiation, both from natural and artificial sources. If it will fail to inform you of the contamination of your food or water, it will successfully notice TODO.

TODO Rapid introduction of the human radiation dose. The Sievert unit.
https://upload.wikimedia.org/wikipedia/commons/2/20/Radiation_Dose_Chart_by_Xkcd.png

Ok, enough radioactivity speech, place to electronic and code. The Pocket Geiger comes as a board with four pins: two are for the usual alimentation stuff (`+V`, `GND`) and the two other for the signals (`SIG`, `NS`). When the sensor is hit by a radiation, it will simply pull the radiation pin (`SIG`) to an high voltage level for some microseconds. But as the photodiode sensor is sensible to vibration, Radiation Watch also added an accelerometer to alert us through the (`NS`) so we can dismiss false-positives.

{% include figure.html img="/assets/2015-12-06-radiation-watch-arduino/setup_photo.jpg" url="/assets/2015-12-06-radiation-watch-arduino/setup_photo.jpg" alt="Pocket Geiger connected to the Arduino" caption="Wiring the Pocket Geiger is not a big deal: weld four wires on the Pocket Geiger card and connect them to your Arduino. Done!" %}

When your Pocket Geiger is connected, you'll need an Arduino firmware to make something with it. Radiation Watch provides [sample code][rw_sample_code] to log the measurements to the serial port. To ease integration Thomas W. has released [a lib][thomasaw_lib]. I've followed the [Toumal work][toumal_lib] to make things even better and provides a library with cleaned code, reduced memory footprint and documented [examples][lib_examples], so you can start rapidly hacking your solution.

The library is available [on GitHub][apg_lib] and released under the MIT license. It comes with an handy [Python script][python_script] to plot the radiation dose in real-time from your serial port, or with a [sample sketch][sd_sketch] to use the Ethernet shield to log the data on an SD card.

Finally all this is good, and for one purpose: measure the background radiation level. We'll see here what it can gives you.

TODO No instant results: need at least 2 min to stabilize

I've first done background radiation measurement in at the first-floor of a house in Colombes, near Paris:

{% include figure.html img="/assets/2015-12-06-radiation-watch-arduino/colombes_2015_12_01_radiation.svg" url="https://plot.ly/~tournadey/15/colombes-1st-december-2015-gamma-radiation/" alt="Plot of the radiation level at Colombes the 1st December 2015" linkTitle="Click to see the data on Plotly" %}

Surprisingly I was able to measure radiation being in a train. I expected the vibration to skew entirely the results, but it wasn't the case. The accelerometer wasn't triggering the noise detection pin and the measured values are sensible ones:

{% include figure.html img="/assets/2015-12-06-radiation-watch-arduino/bordeaux_agen_train_2015_12_02_radiation.svg" url="https://plot.ly/~tournadey/30/bordeaux-to-agen-on-train-2nd-december-2015-gamma-radiation/" alt="Plot of the radiation level on the train from Bordeaux to Agen the 2nd December 2015" linkTitle="Click to see the data on Plotly" %}

TODO Results in my family house in the France South-West countryside.

TODO Histogram // Standard deviation on results?

TODO Golfech measurements, local nuclear power plant

Since this device seems good for background monitoring, I'll connect it to my Raspberry Pi to plot the data online in real-time. Stay tuned.

[effi_synchrone]: http://www.effi-synchrone.com
[rw]: http://www.radiation-watch.org/
[rw_ks]: https://www.kickstarter.com/projects/1517658569/smart-radiation-detector/description
[rw_userreports]: http://www.radiation-watch.org/p/usersreports.html
[rw_boars]: http://www.radiation-watch.org/2013/08/asahi-shinbun-featured-pocketgeiger.html
[X100_datasheet]: http://www.mouser.com/ds/2/313/X100-7_SMD_501401-586455.pdf
[marie_curie_death]: https://en.wikipedia.org/wiki/Marie_Curie#Death
[radium_girls]: https://en.wikipedia.org/wiki/Radium_Girls
[radon_house]: https://en.wikipedia.org/wiki/Radon#Accumulation_in_buildings
[alter_matter]: https://en.wikipedia.org/wiki/Ionizing_radiation
[bg_rad]: https://en.wikipedia.org/wiki/Background_radiation
[spectrum]: https://en.wikipedia.org/wiki/Electromagnetic_spectrum
[electronvolt]: https://en.wikipedia.org/wiki/Electronvolt
[rw_sample_code]: http://radiation-watch.sakuraweb.com/share/ARDUINO.zip
[thomasaw_lib]: https://github.com/thomasaw/RadiationWatch
[toumal_lib]: https://github.com/Toumal/RadiationWatch
[apg_lib]: https://github.com/MonsieurV/ArduinoPocketGeiger
[rw_uk_faqs]: http://www.radiation-watch.co.uk/faqs
[python_script]: https://github.com/MonsieurV/ArduinoPocketGeiger#plot-in-real-time-with-python
[sd_sketch]: https://github.com/MonsieurV/ArduinoPocketGeiger/blob/master/examples/SdCardCsvLogger/SdCardCsvLogger.ino
