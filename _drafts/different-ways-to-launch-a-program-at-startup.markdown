---
layout: post
author: yoan
title: "Seven Different Ways To Run A Program At Startup"
categories: [sysadmin]
tags: [Sysadmon, Linux, Unix, Daemon]
brief: "A comprehensive guide to run a programs at boot time on Unix distributions."
lovehatefeedback: true
draft: true
hacker_news: https://news.ycombinator.com/item?id=11557638
---

In a [previous article](2016/04/23/debian-program-at-boot-time/) I was presenting a pragmatic way to start a program at boot time on a Debian-like OS. While the presented solution - using Monit - is effective, I was meditating on the fact that my article was not... a great factual introduction on how to create daemons!

Also, it missed the opportunity to present the alternative ways of running a program at startup. So here we are again: this time for a more complete review of the use of various init systems. And other trickier ways to do the job.

#### What's This <a href="https://www.youtube.com/watch?v=p_ZxDNZjzVk">Daemon</a>{%include footnote_ref.html number="1" %}?


First, if we want to have a program running at startup, we need it to be a real daemon process.

For the purpose of that article, we'll take this simple - yet essential - Python program:

(Find another example, as printing in a daemon is not very... smart. Yeah, but we can got the log anyway, so that's not too bad.)

{% highlight Python %}
import time
import datetime
periodOfLife = 10

while True:
  print("It is {0} - that's {1} seconds less to live.".format(
    datetime.datetime.now().strftime('%H:%M:%S'), periodOfLife))
  time.sleep(periodOfLife)
{% endhighlight %}

Which will output something like this:

{% highlight plaintext %}
It is 11:07:22 - that's 10 seconds less to live.
It is 11:07:32 - that's 10 seconds less to live.
It is 11:07:42 - that's 10 seconds less to live.
It is 11:07:52 - that's 10 seconds less to live.
It is 11:08:02 - that's 10 seconds less to live.
{% endhighlight %}

Admittedly, this program is a bit depressing. A real daemon.

Well... in fact... not yet!

If we stick to the Richard Steven definition{%include footnote_ref.html number="2" %}, an Unix daemon have the following properties:

* Being Nice ...
* ... Steady ...
* ... And Slow!

https://en.wikipedia.org/wiki/Daemon_(computing)#Creation

The most important point is:

<centerheadlinelikegaryhalbert>
a daemon is not simply a process running in background
</centerheadlinelikegaryhalbert>

If we take our Python program and launch it in background with `python daemonOfLife.py &`, it will have the following side-effects:

* The process will output to the terminal
* The process will be subject to control from the terminal

If we exit our terminal... the process will be killed! This is actually very logical, as our process is a children of the terminal process. The latter being killed - the former is subsequently killed // the former too.

#### Yeah Sure! But How Do You Create This Daemon?


In practical how's a deamon created? See http://stackoverflow.com/questions/3430330/best-way-to-make-a-shell-script-daemon
http://www.itp.uzh.ch/~dpotter/howto/daemonize

So how to deamonize.

* By coding (see up)
* Unix daemonize http://linux.die.net/man/1/daemonize http://software.clapper.org/daemonize/ http://linux.die.net/man/1/daemonize https://github.com/bmc/daemonize
* Nohup? http://linux.die.net/man/1/nohup http://stackoverflow.com/questions/525247/how-do-i-daemonize-an-arbitrary-script-in-unix
* Debian start-stop-deamon http://manpages.ubuntu.com/manpages/xenial/en/man8/start-stop-daemon.8.html
* Unix daemon http://man7.org/linux/man-pages/man3/daemon.3.html http://libslack.org/daemon/manpages/daemon.1.html http://www.libslack.org/daemon/ http://blog.terminal.com/using-daemon-to-daemonize-your-programs/

# The Seven Ways To Run Your Program At Startup:
* Unix daemontools http://cr.yp.to/daemontools.html https://isotope11.com/blog/manage-your-services-with-daemontools https://en.wikipedia.org/wiki/Daemontools http://blog.rtwilson.com/how-to-set-up-a-simple-service-to-run-in-the-background-on-a-linux-machine-using-daemontools/
* SysV init.d (Debian, Slackware, RedHat) https://debian-administration.org/article/28/Making_scripts_run_at_boot_time_with_Debian http://www.slackware.com/config/init.php https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/5/html/Installation_Guide/s1-boot-init-shutdown-sysv.html
* Systemd (most current Linux distribution, even ArchLinux, except last resistants like Slackware) http://unix.stackexchange.com/questions/47695/how-to-write-startup-script-for-systemd https://wiki.debian.org/systemd
* Upstart (Ubuntu 9.10 to Ubunto 14.10, RedHat and CentOS 6) http://upstart.ubuntu.com/getting-started.html
The More Indirect But Good ways
* Monitd https://mmonit.com/monit/
* Supervisor http://supervisord.org/
The Weird But Still Effective Ways
* Docker https://www.docker.com/
The Weird But Not That Smart Ways
* Cron
* https://www.raspberrypi.org/documentation/linux/usage/rc-local.md
The Surely Not Great Ways
* Login-Based Approach (~/.bash_profile, .bashrc and co) (Not a Real Deamon)
* Login-Based Approach on Desktop mode (GNOME ~/.config/autostart/, Unity?) (Not a Real Deamon)

Table with pros/cons, compatibility (Systemd/SysV, Unix/Linux/Mac). Like for findpeaks.


Other source:
* A good overview for Debian: http://unix.stackexchange.com/questions/106656/how-do-services-in-debian-work-and-how-can-i-manage-them
* Complete overview for ArchLinux: https://wiki.archlinux.org/index.php/autostarting

Where to publish my article:
* http://stackoverflow.com/questions/15934729/best-tool-to-code-a-linux-daemon-that-runs-indefinitely

<br>

##### Notes

{% include footnote.html number="1" note="Sorry for the cheesy music, I just can't help myself." %}

{% include footnote.html number="2" note="As defined in W. Richard Stevens' 1990 book, _Unix Network Programming_ (Addison-Wesley, 1990). [See source](https://books.google.fr/books?id=ptSC4LpwGA0C&pg=PA363#v=onepage&q&f=false)." %}
