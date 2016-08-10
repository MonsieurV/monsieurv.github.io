---
layout: post
author: yoan
title: "Run Your Programs at Startup on Unix Distributions"
categories: [sysadmin]
tags: [Embedded systems, data logging, radiation, Geiger counter, open-data, Safecast, radiation]
brief: "This great monit shepherd will prevent you to fight against systemd."
lovehatefeedback: false
draft: false
hacker_news: https://news.ycombinator.com/item?id=11557638
---

When I play with my Raspberry Pi or host apps on my server, it always comes a time when I want my programs to automatically run at boot time. Depending on the Debian version or on the requirements the tools change, and each time I see myself searching the web for a way to do it properly. And that's not a life! A so common need should be easier to fulfilled.

#### The GNU/Linux world customs

The Raspberry Pi tutorials often mention the [rc.local](https://www.raspberrypi.org/documentation/linux/usage/rc-local.md) file for this purpose, or even the usage of [cron](https://www.raspberrypi.org/documentation/linux/usage/cron.md). What's missing with these methods is a way to control the lifecycle of our script after it have been first launched at startup. How then to stop or restart the service?

The common sysadmin way of doing that is to use the init system of your distribution. Typically on a Debian-like you have used init.d scripts and [update-rc.d](https://www.debian-administration.org/article/28/Making_scripts_run_at_boot_time_with_Debian) to schedule your script at boot time. That works fine, even if I can't help Googling the magic commands.

Here the tricky thing: with the large adoption of systemd as the new init system you'll have to learn a new way of doing that, which is a bit [simpler](http://unix.stackexchange.com/questions/47695/how-to-write-startup-script-for-systemd), but still doesn't resolve the Googling issue when you doing that each 3 months.

And with all these configuration files we still don't have monitoring features{%include footnote_ref.html number="1" %}.

#### But what we need is a dog

To make our programs start at boot time, but also continue running thereafter, no other thing is better that a dog that can watch and bark at our processes.

{% include figure.html img="https://mmonit.com/monit/img/logo.png" caption="Ceci n'est pas un chien." %}

I found [monit](https://mmonit.com/monit/) to be straightforward-enough to set up and a versatile tool that you can turn around your needs. Also it has the huge pro to work on most Unix distributions, so we don't have to Google for a solution again and again each time we change of playground.

Once installed, you can directly configure your monitored services in `/etc/monit/conf.d/`.

To ensure a process is always running based you simply specify its pid file and the start and stop scripts, you can for example create a `radbox-web` configuration file in `/etc/monit/conf.d/`:

{% highlight sh %}
check process radbox-web with pidfile /home/pi/PiRadBox/run.pid
    start program = "/usr/bin/make -C /home/pi/PiRadBox/ start"
    stop program  = "/usr/bin/make -C /home/pi/PiRadBox/ stop"
{% endhighlight %}

Yes you need a PID file to store the PID of the process that you want to monitor. In the example I use `make` to launch my program, which is pretty cool because... well, who does not know how to write a simple Makefile? In the negative case [Google](http://lmgtfy.com/?q=write+a+makefile)'s still here.

Just keep in mind to properly populate the PID file each time you start your program. Here a simple example how you can do it:

{% highlight makefile %}
PID=$(shell cat run.pid)

start:
    /usr/bin/python -u app.py & echo $$! > run.pid

stop:
    kill -2 ${PID}
{% endhighlight %}

Then you can reload monit to enable the monitoring for the newly created service and check that all is running:

{% highlight sh %}
monit monitor reload
monit status
{% endhighlight %}

Which will gives you something like this great this great [output](https://gist.github.com/MonsieurV/c283ae5d993a459ad112e29dc4a3232c#file-monit-output).

You may need to uncomment the following lines in your `/etc/monit/monitrc` configuration file to be able to speak with the monit deamon from the command line:

{% highlight conf %}
set httpd port 2812 and
    use address localhost  # only accept connection from localhost
    allow localhost        # allow localhost to connect to the server and
{% endhighlight %}

#### To watch on our web herd

Monit comes with a complete set of monitoring features, so you can not only check that the process is running, but also that it is doing sensible operations.

In a web context you can check you're web server is up and returns you something else than 500s:

{% highlight sh %}
check process radbox-web with pidfile /home/pi/PiRadBox/run.pid
    start program = "/usr/bin/make -C /home/pi/PiRadBox/ start"
    stop program  = "/usr/bin/make -C /home/pi/PiRadBox/ stop"
    if failed
       host 127.0.0.1 port 80 protocol http
       and status = 200
       and request / with content = "RadBox"
    then alert
{% endhighlight %}

For each rules we can choose to throw an alert, to restart the service or whatever action you have defined. If you run dozen of services and some critical ones alerts can be a great deal to help you go out or sleep without over-worrying... until you receive a mail from monit!

For more complex monitoring cases you can dig in the tool, which contains a good set of what is commonly needed. But in the end of the day if you really want to play with this dog you better go read [the fucking manual](https://mmonit.com/monit/documentation/monit.html) than stay here.

<br>

##### Notes

{% include footnote.html number="1" note="That's unfair for systemd, as it does have [monitoring features](https://serversforhackers.com/video/process-monitoring-with-systemd), and even embeds a watchdog protocol named [sd_notify](https://www.freedesktop.org/software/systemd/man/sd_notify.html). But the watchdog is not easy to [use](https://gist.github.com/Spindel/1d07533ef94a4589d348) and is intrusive. And life's unfair." %}
