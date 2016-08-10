---
layout: post
author: yoan
title: "A Geiger Counter for Your House - Part 2"
categories: [Embedded systems]
tags: [Embedded systems, data logging, Raspberry Pi, Geiger counter, radiation]
brief: "Keep an eye on those sneaky gamma rays using your Raspberry Pi."
lovehatefeedback: false
draft: false
hacker_news: https://news.ycombinator.com/item?id=11260709
---

Finally the time has come for the follow-up of this series about the Radiation Watch Pocket Geiger counter.

In [the first part][part_one] we were logging the Pocket Geiger measurements to an SD card with an Arduino. As my title promises a Geiger counter for your house, we need to do better! Like something actually connected.

#### Getting connected with Raspberry Pi

Indeed it would be great to post the radiation level online in real-time. To realize this mission we need a board that can be easily connected to internet, and that is obviously able to speak with our Pocket Geiger. Guess what? This is exactly what Raspberry Pi is great at!

Instead of connecting our Pocket Geiger to an Arduino we will wire it to the Raspberry Pi GPIO ports. We will benefit the Raspberry Pi generous package: Ethernet and USB ports, so the network access is simple ; HDMI output, so we can check on a screen what's going on. The Raspberry Pi [ARM chip](https://www.arm.com/products/processors/classic/arm11/arm1176.php) is also way more powerful than the Arduino Uno [ATmega328 microcontroller](http://www.atmel.com/devices/atmega328p.aspx): it can honorably run a whole GNU/Linux distribution. With the dedicated [Raspbian OS](https://www.raspbian.org/) - a Debian-fork for... yes Raspberry Pi! - we will enjoy a familiar and welcoming environment.

{% include figure.html img="/assets/2016-03-04-radiation-watch-raspberry/raspberry-pi-and-pocket-geiger-1.png" caption="Connecting the Pocket Geiger requires a bit of soldering to attach wires on the Pocket Geiger board. Then you plug the other part of the wires to the Raspberry Pi GPIO pins and you're ready to go!" alt="Wiring of Pocket Geiger and Raspberry Pi" %}

Having the Pocket Geiger linked to the Raspberry Pi is exciting, however that doesn't bring much in itself. Without software hardware's of no use! We need a way to communicate with the Pocket Geiger and get back the readings to our Raspberry Pi.

#### Have hardware, need software

As I haven't found something that really convinced me{%include footnote_ref.html number="1" %}, I developed a Python library to communicate with the PocketGeiger: please welcome the [PiPocketGeiger library][PiPocketGeiger_lib].

The PiPocketGeiger library is built on top of the [RPi.GPIO][rpi_gpio_lib] package. It uses [edge detection][rpi_gpio_irq] interrupts to [count](https://github.com/MonsieurV/PiPocketGeiger/blob/22f29b0a3c3e5f46a8afa1e37b82a58c012ae456/PiPocketGeiger/__init__.py#L102) the ionization events and periodically [processes](https://github.com/MonsieurV/PiPocketGeiger/blob/22f29b0a3c3e5f46a8afa1e37b82a58c012ae456/PiPocketGeiger/__init__.py#L119) the current dose in a thread. This is roughly a Python rewrite of the [Arduino library](https://github.com/MonsieurV/ArduinoPocketGeiger) C code.

A major difference with the Arduino version is that Linux is not a hard real-time OS{% include footnote_ref.html number="2" %}, so we have no guarantee we're effectively monitoring the edge falling in a timely manner. That said the timing of an interrupt from the Pocket Geiger{% include footnote_ref.html number="3" %} makes it unlikely that we miss a wild Gamma ray. However I'm not competent enough on the subject to be definitive.

#### Install the Gamma Ball

You can install the library using `pip`. The RPi.GPIO package needs root privileges to access to the hardware, I thus recommend to install the lib root-wide:

{% highlight shell %}
sudo install pip PiPocketGeiger
{% endhighlight %}

That done you can use the lib in your Python script. You firstly initialize the lib to indicates which GPIO pins to read from. Then you can get the current background computed radiation level whenever you need:

{% highlight python %}
# We initialize the lib with respectively the radiation signal and noise pins number.
with RadiationWatch(24, 23) as radiationWatch:
    print(radiationWatch.status())
    # {'duration': 14.9, 'uSvh': 0.081, 'uSvhError': 0.081, 'cpm': 4.29}
{% endhighlight %}

For those that already ask for reactive applications, yes you can register callbacks that will be triggered when a gamma ray drop on the Pocket Geiger, or when there is noisy events:

{% highlight python %}
def onRadiation():
    print("Ray appeared!")
def onNoise():
    print("Vibration! Stop moving!")
with RadiationWatch(24, 23) as radiationWatch:
   radiationWatch.registerRadiationCallback(onRadiation)
   radiationWatch.registerNoiseCallback(onNoise)
   while 1:
       time.sleep(1)
{% endhighlight %}

#### Real-time online publishing

To truly benefit the power of our Raspberry Pi the library provides examples to publish the data in live on various web services.

First typical application we can live-tweet the radiation level in our house, so nobody miss you're hacking:

<blockquote class="twitter-tweet" data-lang="fr"><p lang="en" dir="ltr">Radiation in my house (Laussou, France): 0.059 uSv/h +/- 0.012 -- 3.12 CPM <a href="https://twitter.com/radiation_watch">@radiation_watch</a></p>&mdash; Yoan Tournade (@tournadey) <a href="https://twitter.com/tournadey/status/676932050562232320">16 décembre 2015</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

Instead of simply logging to a file on your Rapsberry Pi you can log to your Google Drive account:

{% include iframe.html src="https://docs.google.com/spreadsheets/d/1tmT0lyzA3szkDKzPLKNK2xI0aqB33GnsbCKRP8-fwiM/pubhtml?gid=0&amp;single=true&amp;widget=true&amp;headers=false" url="https://docs.google.com/spreadsheets/d/1tmT0lyzA3szkDKzPLKNK2xI0aqB33GnsbCKRP8-fwiM/edit?usp=sharing" caption="Logging measurements to a Google Docs Sheet" %}

These examples are fine to keep trace of raw data, but not quite handy to visualize it. We can use [Plotly](https://plot.ly/), a great service to create and share plots:

{% include figure.html img="/assets/2016-03-04-radiation-watch-raspberry/plotly-live.gif" url="https://plot.ly/~tournadey/137/radiation-dose-gamma-rays/" caption="Live plot of the radiation dose at my country house, in the South-West of France" alt="Live plot of the radiation dose at my country house." linkTitle="Click to see the data on Plotly" %}

For the next part we will make all that more open - I mean more open-data oriented. Coming soon. Hopefully. Yeah we're pretty lazy here.

Edit March 30th, 2016: [Here](/2016/03/30/radiation-watch-safecast/) it is!

<br>

##### Notes

{% include footnote.html number="1" note="There is this [C driver](https://github.com/orsp/Pocket_Rasdiation_Counter) that seems to work fine (haven't tested it), but I wanted a pure Python interface and a lib that can be easily distributed and installed with pip." %}

{% include footnote.html number="2" note="This comment apply only for the standard Raspbian distribution, has it does exist a patched version of Linux for real-time applications: the [RT-Preempt Patch](https://rt.wiki.kernel.org/index.php/RT_PREEMPT_HOWTO), that gives hard real-time capabilites to the kernel. You may also check the [Xenomai](https://xenomai.org/start-here/) or [RTLinux](https://en.wikipedia.org/wiki/RTLinux) projects, which each kind of make cohabit a hard real-time kernel with the Linux one, ensuring the whole can be preempted in a deterministic way." %}

{% include footnote.html number="3" note="The Radiation Watch [sample code](https://github.com/thomasaw/RadiationWatch/blob/434bdc1e6cb7db0c979bdd6e9130951e9c7fc689/RadiationWatch.cpp#L68) says the Radiation pulse normally keeps low for about 100 microseconds." %}

[part_one]: /2015/12/06/radiation-watch-arduino/
[PiPocketGeiger_lib]: https://github.com/MonsieurV/PiPocketGeiger
[rpi_gpio_lib]: https://pypi.python.org/pypi/RPi.GPIO
[rpi_gpio_irq]: https://sourceforge.net/p/raspberry-gpio-python/wiki/Inputs/#interrupts-and-edge-detection
