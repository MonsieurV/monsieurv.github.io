---
layout: post
author: yoan
title: "A Geiger counter for your house - Part 2"
categories: [Embedded systems]
tags: [Embedded systems, data logging, Raspberry Pi, Geiger counter, radiation]
brief: "Keep an eye on those sneaky gamma rays using your Raspberry Pi."
lovehatefeedback: true
draft: true
---

Finally I've made time to write the follow-up of this series about the Radiation Watch Pocket Geiger.

In [the first part][part_one] we were logging the radiation level offline using an Arduino with an SD card. As my title promises a Geiger counter for your house, we need to do better! Actually something more connected.

It will be great to post the radiation level online and in real-time. For that we need a board that we can easily connect to internet, but also still still be linked to our Pocket Geiger. Guess what? This is exactly what Raspberry Pi is great at!

So instead of connecting our Pocket Geiger to an Arduino board, we will wire it to the Raspberry Pi GPIO ports. We will benefit the Raspberry Pi generous package: Ethernet and USB ports, so the network access is simple ; it also have HDMI if you want to see what's you're doing on a screen. The computing power of the ARM chip is also way above the Arduino Uno ATmega328 microcontroller, so it can run a whole GNU/Linux distribution. We will enjoy the environment to rapidly hack some apps with Python.

{% include figure.html img="/assets/2016-03-04-radiation-watch-raspberry/raspberry-pi-and-pocket-geiger-1.png" caption="Connecting the Pocket Geiger to a Raspberry Pi also requires a bit of soldering to attach wires on the Pocket Geiger board. Then you plug the wires to the GPIO pins and you're ready to go!" alt="Wiring of Pocket Geiger and Raspberry Pi" %}

The global picture is good, however we need a way to communicate and get the readings from the Pocket Geiger to our Raspberry Pi. As I found nothing convincing for that, I've developed a Python library for the purpose: please welcome [the PiPocketGeiger lib][PiPocketGeiger_lib].

The PiPocketGeiger library is built on top of the [RPi.GPIO] package. It uses edge signal interrupt to count the radiation and detect noisy condition.

Linux is not a proper real-time OS (note: that remarq apply only for the standard Raspbian distribution, has it do exist patched versions of Linux for real-time applications: TODO), so we have no garanty we're effectively monitor the edge falling in a timely manner. That said, with the use of interrupts the issue is less dramatic, and I have not observed any sensible variation in the readings compared to the Arduino library. TODO Make a live recording with RPi and Arduino in paralell.

You can install it on your Raspberry Pi using `pip`. The RPi.GPIO package need root privileges to read the pins, I thus recommend to install the lib root-wide:

```sudo install pip PiPocketGeiger
```

That done you can use the lib in your Python script, with only one line to initialize indicate the GPIO pins on which the Pocket Geiger is connected. Then you can get the current radiation level whenever you need.

```
# We initialize the lib with respectively the radiation signal and noise pins number.
with RadiationWatch(24, 23) as radiationWatch:
    print(radiationWatch.status())
    # {'duration': 14.9, 'uSvh': 0.081, 'uSvhError': 0.081, 'cpm': 4.29}
```

For those that already imagine reactive applications you can also register a callbacks that will be triggered in case of a radiation event, or when there is too much noise:

```
def onRadiation():
    print("Ray appeared!")
def onNoise():
    print("Vibration! Stop moving!")
with RadiationWatch(24, 23) as radiationWatch:
   radiationWatch.registerRadiationCallback(onRadiation)
   radiationWatch.registerNoiseCallback(onNoise)
   while 1:
       time.sleep(1)
```

To truly benefit the power of our Raspberry Pi we have made scripts to publish on live the data on web services.

First typical application we can live-tweet the radiation level in our house to Twitter:

<blockquote class="twitter-tweet" data-lang="fr"><p lang="en" dir="ltr">Radiation in my house (Laussou, France): 0.059 uSv/h +/- 0.012 -- 3.12 CPM <a href="https://twitter.com/radiation_watch">@radiation_watch</a></p>&mdash; Yoan Tournade (@tournadey) <a href="https://twitter.com/tournadey/status/676932050562232320">16 décembre 2015</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

Instead of logging to a file on your Rapsberry Pi you can also log to your Google Drive account:

{% include iframe.html src="https://docs.google.com/spreadsheets/d/1tmT0lyzA3szkDKzPLKNK2xI0aqB33GnsbCKRP8-fwiM/pubhtml?gid=0&amp;single=true&amp;widget=true&amp;headers=false" %}

https://docs.google.com/spreadsheets/d/1tmT0lyzA3szkDKzPLKNK2xI0aqB33GnsbCKRP8-fwiM/edit?usp=sharing

If that's good to keep trace and share data, this is not an ideal way to visualize it. There is a great service named Plotly on the web to create and share plots:

{% include figure.html img="/assets/2016-03-04-radiation-watch-raspberry/plotly-live.gif" url="https://plot.ly/~tournadey/137/radiation-dose-gamma-rays/" caption="Live plot of the radiation level at my country house, in the South-West of France" alt="Live plot of the radiation level at my country house." linkTitle="Click to see the data on Plotly" %}


RPi: propose a built-case for 200 €. Monitore (to Safecast? to a backend of mime? to a custom endpoint?) as soon as you connect it to internet (Ethernet with DHCP).


https://plot.ly/~tournadey/137




[part_one]: /2015/12/06/radiation-watch-arduino/
[PiPocketGeiger_lib]: https://github.com/MonsieurV/PiPocketGeiger
