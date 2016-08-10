---
layout: post
author: yoan
title: "A Geiger Counter for Your House - Part 1"
categories: [Embedded systems]
tags: [Embedded systems, data logging, Arduino, Geiger counter, radiation]
brief: "Play with an Arduino and the Radiation Watch Pocket Geiger to monitor radiation."
draft: false
lovehatefeedback: false
hacker_news: https://news.ycombinator.com/item?id=10702855
---

Some month ago a [client of mime][effi_synchrone] offered me a Geiger counter kit from [the Radiation Watch project][rw]. The last few weeks I was able to play with it.

{% include figure.html img="/assets/2015-12-06-radiation-watch-arduino/pocket_geiger.png" alt="Pocket Geiger illustration" caption="In the white little box resides the Pocket Geiger board, with its photodiode sensor." %}

Radiation Watch is a scientific and citizen initiative born after the Fukushima Daiishi disaster and which has been funded through [Kickstarter][rw_ks] in July 2011. It aims to provide a cheap radiation detector that [anyone can use][rw_userreports], even [boars][rw_boars].

Initially conceived to be connected to an iPhone, Radation Watch now provides an embedded version of the Pocket Geiger. Whatever version is used, the board includes a [X100-7 PIN photodiode][X100_datasheet] from FirstSensor for [gamma-ray detection][rw_uk_faqs]. It is thus not a proper [Geiger-Müller][gm_tube] counter.

#### What are these rays?

In you don't remember well we usually classify radiations under three hats: alpha, beta and gamma rays. Alpha and beta radiation are charged particles, whereas gamma rays are photons of electromagnetic energy, with no charge and mass. They are all three considered ionizing radiation - which means they can [alter][alter_matter] the matter they go through -, but have very different penetrating and ionizing abilities due to their respective mass, size and nature.

Alpha and beta radiation can be stopped by an aluminum foil: if you don't [play with radioactive sources][marie_curie_death] or [ingest contaminated substances][radium_girls] you hopefully won't be exposed to significant level of alpha and beta rays, except maybe from [the radon in your house][radon_house]. This contrasts with the very high frequency electromagnetic nature of gamma rays, giving them a super high ability to pass through matter: imagine your Wifi on steroids, with a frequency of above [10 exahertz][spectrum] and an [energy][electronvolt] more than 100,000,000 times greater. Yes, it can cooks your DNA like your microwave boils your Chinese noodles.

{% include figure.html img="/assets/2015-12-06-radiation-watch-arduino/radiation_penetration.svg" url="https://en.wikipedia.org/wiki/Radiation" alt="Alpha, beta and gamma radiation penetration" caption="The ability to penetrate matter and ionize cells depends on the radiation type (image from Wikipedia)." %}

When you consider [background radiation][bg_rad] monitoring, detecting only gamma rays gives you a sensible indication of your exposition to radiation, from both natural and artificial sources. If it will fail to detect the contamination of your food or water, it will help you determine the environment radiation level and its changes over time. Cosmic rays are chasing [your computer][cosmic_ray_electronics]? Doubtful steam [comes out][tmii] of the house of your maybe-too-genius neighbor? You'll have a chance to really know.

Speaking of ionizing radiation and its effect on human body, we use the sievert unit to quantify a dose. One sievert is bad enough to significantly increases chances of cancer (see this great [dose chart][dose_chart]). Since we don't usually absorb high-level dose in a [one shot manner][al_poisoning], we associates the unit with a time dimension when we do background measurements: e.g. uSv/h, for the amount of ionizing radiation you will received staying at a place for one hour.

#### How to catch them?

Ok, enough radioactivity blabla, place to electronic and code. The Pocket Geiger board comes with four pins: two are for the usual alimentation stuff (`+V`, `GND`) and the two others for the signals (`SIG`, `NS`). When the sensor is hit by a radiation, it will simply pull the radiation pin (`SIG`) to an high voltage level for some microseconds. But as the photodiode sensor is sensible to vibrations, Radiation Watch also included an accelerometer so we can get notice of them through the noise pin (`NS`) and dismiss the corresponding false-positives.

{% include figure.html img="/assets/2015-12-06-radiation-watch-arduino/setup_photo.jpg" url="/assets/2015-12-06-radiation-watch-arduino/setup_photo.jpg" alt="Pocket Geiger connected to the Arduino" caption="Assembling the whole is not a big deal: weld four wires on the Pocket Geiger card and connect them to your Arduino. Done!" %}

When your Pocket Geiger is wired, you'll need an Arduino firmware to do something with it. Radiation Watch provides [sample code][rw_sample_code] to log measurements through the Arduino serial port. To ease integration with other software Thomas Weisbach has released [a library][thomasaw_lib]. I've followed the [Toumal work][toumal_lib] to make things even better and provides a library with cleaned code, reduced memory footprint and documented examples, so you can start rapidly hacking your things.

The library is available [on GitHub][apg_lib] and released under the MIT license. It comes with a handy [Python script][python_script] to plot the radiation dose in real-time from your serial port, or with a [sample sketch][sd_sketch] for logging data on an SD card thanks to the Ethernet shield.

To use it, you need to create a `Radiation Watch` object with the pin and matching IRQ numbers to which are connected your Pocket Geiger:

{% highlight cpp %}
RadiationWatch radiationWatch(signPin, noisePin, signIrq, noiseIrq);
{% endhighlight %}

This object must be initialized at the Arduino setup phase with `RadiationWatch::setup()`. In your program main loop you also need to run the `RadiationWatch::loop()` frequently in order to compute the statistics.

Then you can get the radiation level whenever you need using `RadiationWatch::uSvh()`, with the current incertitude specified by `RadiationWatch::uSvhError()`. You can also register callbacks that will be called in case of radiation or vibration occurrence, with respectively `RadiationWatch::registerRadiationCallback()` and `RadiationWatch::registerNoiseCallback()`.

Complete examples can be found [here][apg_geiger_samples].

#### Gotta catch 'em all!

Finally all this is good, but for one purpose: measuring the background radiation level. Let's see what kind of data we can get from it.

I've first done background radiation measurement at the first floor of a house in Colombes, near Paris:

{% include figure.html img="/assets/2015-12-06-radiation-watch-arduino/colombes_2015_12_01_radiation.svg" url="https://plot.ly/~tournadey/96/colombes-1st-december-2015/" alt="Plot of the radiation level at Colombes the 1st December 2015" linkTitle="Click to see the data on Plotly" %}

Surprisingly I was able to measure radiation being in a moving train. I expected the vibrations to skew entirely the results, but it wasn't the case. The accelerometer wasn't triggering the noise detection pin and the measured values are sensible ones:

{% include figure.html img="/assets/2015-12-06-radiation-watch-arduino/bordeaux_agen_train_2015_12_02_radiation.svg" url="https://plot.ly/~tournadey/91/bordeaux-to-agen-on-train-2nd-december-2015/" alt="Plot of the radiation level on the train from Bordeaux to Agen the 2nd December 2015" linkTitle="Click to see the data on Plotly" %}

In my family house, somewhere in the south-west countryside, we're a little more exposed, but still way not enough to turn Hulk.

{% include figure.html img="/assets/2015-12-06-radiation-watch-arduino/laussou_2015_12_06.svg" url="https://plot.ly/~tournadey/105/laussou-6th-december-2015/" alt="Plot of the radiation level in Laussou the 6th December 2015" linkTitle="Click to see the data on Plotly" %}

Since I don't have any radioactive source (for the happy folks living in USA, you can freely buy and possess [little ones][imagesco_sources]) nor the time to go in the volcanic parts of France, I've gone to my local nuclear power plant, as a last attempt to measure something interesting. But outside an undamaged [containment building][containment_building] and at a view-distance from the plant, that's not very exciting.

{% include figure.html img="/assets/2015-12-06-radiation-watch-arduino/golfech_north_plant_2015_12_06.svg" url="https://plot.ly/~tournadey/76/golfech-1-km-north-of-the-power-plant-6th-december-2015/" alt="Plot of the radiation level under 1 Km of the Golfech nuclear power plant the 6th December 2015." linkTitle="Click to see the data on Plotly" caption="Nothing special here." %}

You have maybe noticed the Pocket Geiger is not accurate enough to give instant results: it need at least two minutes to reduce the error incertitude and stabilize the readings. You can spot this on the graphs: the first results are highly dispersed, with a great incertitude range.

Since this device seems good for background monitoring, I'll connect it to my Raspberry Pi so we can plot the data online in real-time. Stay tuned.

Edit March 14th, 2016: The [second part](/2016/03/04/radiation-watch-raspberry/) is now out!

<br>

**Thanks** to Nicolas C. for reading drafts of this, to Thomas Weisbach for having managed the license issue and to Nicolas Michaux for giving me the idea.

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
[cosmic_ray_electronics]: https://en.wikipedia.org/wiki/Cosmic_ray#Effect_on_electronics
[tmii]: https://en.wikipedia.org/wiki/Three_Mile_Island_accident
[al_poisoning]: https://en.wikipedia.org/wiki/Poisoning_of_Alexander_Litvinenko
[dose_chart]: https://upload.wikimedia.org/wikipedia/commons/2/20/Radiation_Dose_Chart_by_Xkcd.png
[imagesco_sources]: http://www.imagesco.com/geiger/radioactive-sources.html
[containment_building]: https://en.wikipedia.org/wiki/Containment_building
[gm_tube]: https://en.wikipedia.org/wiki/Geiger%E2%80%93M%C3%BCller_tube
[apg_geiger_samples]: https://github.com/MonsieurV/ArduinoPocketGeiger/tree/master/examples
