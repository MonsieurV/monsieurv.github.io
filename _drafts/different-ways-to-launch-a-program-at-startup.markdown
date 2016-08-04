---
layout: post
author: yoan
title: "The 7 Different Ways To Launch A Program At Startup"
categories: [sysadmin]
tags: [Sysadmon, Linux, Unix, Daemon]
brief: "Start automatically our programs at boot time on Unix distributions."
lovehatefeedback: true
draft: true
---

In a previous article I was presenting a pragmatic way to automatically start a program at boot time on a Raspberry Pi Raspbian OS. As it was working, I was meditating on the fact that my article was not... a great factual introduction on how

First, if we want to have a program running at startup, we need it to be a real deamon process.

But after getting any further, man, what's a deamon? We will stick to the Richard Steven definition of Unix deamon. See http://linux.die.net/man/1/daemonize

In practical how's a deamon created? See http://stackoverflow.com/questions/3430330/best-way-to-make-a-shell-script-daemon
http://www.itp.uzh.ch/~dpotter/howto/daemonize

So how to deamonize.
* By coding (see up)
* Unix daemonize http://software.clapper.org/daemonize/ http://linux.die.net/man/1/daemonize https://github.com/bmc/daemonize
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
