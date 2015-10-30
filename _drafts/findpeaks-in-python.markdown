---
title: "Peak detection in the Python world"
categories: [Digital signal processing]
tags: [DSP, Python]
brief: "An overview of the peak detection algorithms in Python."
---

As I was working on a signal processing project, I've come to need an equivalent
of the MatLab `findpeaks` function in the Python world.

Contrary to other MatLab functions which have direct or near-direct equivalent
in the Python Numpy and Scipy packages, searching for peaks in a signal as in
the MatLab DSP toolbox was not obvious to do in Python (has no direct equivalent).
