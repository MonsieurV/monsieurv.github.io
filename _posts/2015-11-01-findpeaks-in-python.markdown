---
layout: post
author: yoan
title: "Peak Detection in the Python World"
categories: [Digital signal processing]
tags: [DSP, Python]
brief: "An overview of the peak detection algorithms in Python."
draft: false
hacker_news: https://news.ycombinator.com/item?id=10519736
---

As I was working on a signal processing project for [Equisense][], I've come to need an equivalent
of the MatLab [`findpeaks` function][findpeaks_ref] in the Python world.

#### Detecting peaks with MatLab

For those not familiar to digital signal processing, peak detection is as easy to understand as it sounds: this is the process of finding peaks - we also names them local maxima or local minima - in a signal. The MatLab DSP Toolbox makes this super easy with its `findpeaks` function. Saying your want to search local maxima in an audio signal, for example [2000 samples][cb_samples] of the Laurent Garnier famous track Cripsy Bacon, all you have to do is:

{% highlight matlab %}
cb = audioread('Crispy_Bacon.wav');
findpeaks(cb(50061:52060), 'MinPeakDistance', 100, 'MinPeakHeight', 0.04)
{% endhighlight %}

{% include figure.html img="/assets/2015-11-01-findpeaks-in-python/matlab_findpeaks.png" caption="MatLab findpeaks in action on an audio sample. We've specified a minimum distance (100 samples) and a minimum height (0.04 amplitude) filters." alt="Plot of results from MatLab findpeaks" %}

We can specify filtering options to the function so the peaks that do not interest us are discarded. All this is great, but we need something working in Python. In a perfect world it will give exactly the same output, so we have consistent results between our Python code and the MatLab code.

#### The Scipy try

Contrary to other MatLab functions that have direct equivalents in the Numpy and Scipy scientific and processing packages, it is no easy task to get the same results from the Scipy [`find_peaks_cwt` function][find_peaks_cwt_ref] that from the MatLab `findpeaks`. Even worse, the wavelet convolution approach of `find_peaks_cwt` is not straightforward to work with: it adds complexity that is of no use for well-filtered and noiseless signals.

{% highlight python %}
import numpy as np
from scipy.signal import find_peaks_cwt
cb = np.array([-0.010223, ... ])
indexes = find_peaks_cwt(cb, np.arange(1, 550))
{% endhighlight %}

{% include figure.html img="/assets/2015-11-01-findpeaks-in-python/find_peaks_cwt.png" caption="Scipy find_peaks_cwt on the same sample. I'm not sure how it works and I was not able to easily specify a minimum peak height filter." alt="Plot of results from Scipy find_peaks_cwt" %}

#### Into the wild

Wondering how to make our algorithms works as simply with Python that they were in MatLab, I've search around the web for other peak detection algorithms available in Python. [Stackoverflow][so_1713335] get me to [`peakdetect`][peakdetect], a translation of a MatLab script. As it is clearly more trivial to use that `find_peaks_cwt`, it still won't give you the same results that the MatLab `findpeaks` function. The algorithm don't find all peaks on low sampled signals or on short samples, and don't have either a support for minimum peak height filter.

{% highlight python %}
import numpy as np
from peakdetect import peakdetect
cb = np.array([-0.010223, ... ])
peaks = peakdetect(cb, lookahead=100)
{% endhighlight %}

{% include figure.html img="/assets/2015-11-01-findpeaks-in-python/peakdetect.png" caption="Sixtenbe peakdetect at work. Easy to use and great results, but miss filtering. Note it detects both local maxima and local minima." alt="Plot of results from peakdetect" %}

#### Octave One

Going ahead I've checkout the GNU Octave project, a processing-intended language quite similar to MatLab. The Octave-Forge repository hosts a digital signal processing package with a [`findpeaks` function][findpeaks_of_ref]. Bingo? Well, yes and no. The function have an appealing interface, with a great filtering support. But this is not a Python project: as you'll find ways to call your Octave distribution from your Python code (see [oct2py][]), it surely won't be effective at large scale and makes the requirements for your code more complex.

{% highlight python %}
import numpy as np
from oct2py import octave
cb = np.array([-0.010223, ... ])
octave.eval("pkg load signal")
(peaks, indexes) = octave.findpeaks(cb, 'DoubleSided', 'MinPeakHeight', 0.04, 'MinPeakDistance', 100, 'MinPeakWidth', 0)
{% endhighlight %}

{% include figure.html img="/assets/2015-11-01-findpeaks-in-python/octave_findpeaks.png" caption="Octave-Forge findpeaks is doing great, with double sided results and extended filtering. But this is not a Python project." alt="Plot of results from Octave-Forge findpeaks" %}

#### The PyPi Wonderland

As I was going to code a Python adaptation of the Octave-Force `findpeaks`, I finally found what I was searching: a Python native equivalent of the MatLab `findpeaks`, with minimum distance and height filtering support. I even found two!

The first is [the PeakUtils package][PeakUtils] by Lucas Hermann Negri which provides 1D peak detection utilities. Its [`indexes`][indexes] function allows you to detect peaks with minimum height and distance filtering.

{% highlight python %}
import numpy as np
import peakutils
cb = np.array([-0.010223, ... ])
indexes = peakutils.indexes(cb, thres=0.02/max(cb), min_dist=100)
{% endhighlight %}

{% include figure.html img="/assets/2015-11-01-findpeaks-in-python/peakutils_indexes.png" caption="The PeakUtils indexes function is easy to use and allows to filter on an height threshold and on a minimum distance between peaks." alt="Plot of results from PeakUtils indexes" %}

#### Going through space

The second is published on a [jupyter notebook][] and is written by Marcos Duarte. Not easy to find, but the greatest so far for what I need: the parameters for filtering are modeled after the MatLab `findpeaks` ones. It comes as a single source file and only depends on Numpy, so it is no big deal to integrate. And more importantly, it will consistently get you the same results than MalLab `findpeaks`!

{% highlight python %}
import numpy as np
from detect_peaks import detect_peaks
cb = np.array([-0.010223, ... ])
indexes = detect_peaks(cb, mph=0.04, mpd=100)
{% endhighlight %}

{% include figure.html img="/assets/2015-11-01-findpeaks-in-python/peakutils_indexes.png" caption="As direct to use as the MatLab findpeaks, the detect_peaks function is a great choice as a Python substitute." alt="Plot of results from Marcos Duarte detect_peaks" %}

To avoid others the same roaming I've put on GitHub [an overview][overview_github] of these findings. Let me know if you got another open-source alternatives so we update the list.

#### Edit 17th November

As Lucas Hermann Negri pointed out on [HN][hn_md_comment], the PeakUtils `indexes` function was actually inspired by Marcos Duarte implementation of `detect_peaks`, explaining the similar results. Moreover he notes that the PeakUtils comes with other convenient utilities, such as `baseline` or `interpolate`.

The [`interpolate` function][interpolate_ref] enhances the peak resolution by fitting Gaussians or computing centroids. Taking the previous example, here how you get it work:

{% highlight python %}
import numpy as np
import peakutils
cb = np.array([-0.010223, ... ])
indexes = peakutils.indexes(cb, thres=0.02/max(cb), min_dist=100)
# [ 333  693 1234 1600]
interpolatedIndexes = peakutils.interpolate(range(0, len(cb)), cb, ind=indexes)
# [  332.61234263   694.94831376  1231.92840845  1600.52446335]
{% endhighlight %}

{% include figure.html img="/assets/2015-11-01-findpeaks-in-python/peakutils_interpolate.png" caption="Near to be insensible in our case, the PeakUtils interpolate function allows a higher peak resolution." alt="Plot of results from PeakUtils interpolate" url="/assets/2015-11-01-findpeaks-in-python/peakutils_interpolate.png" %}

This time before the peak resolution, the [`baseline` function][baseline_ref] will be very handy in presence of drifting signals or to deal with unwanted low-frequency phenomenon: it kind of [high-pass filter][highpass_filter] the signal. The PeakUtils documentation have a [good example][baseline_example] of its use.

[Equisense]: http://www.equisense.com
[findpeaks_ref]: http://fr.mathworks.com/help/signal/ref/findpeaks.html
[find_peaks_cwt_ref]: http://docs.scipy.org/doc/scipy/reference/generated/scipy.signal.find_peaks_cwt.html
[so_1713335]: https://stackoverflow.com/questions/1713335/peak-finding-algorithm-for-python-scipy/
[peakdetect]: https://gist.github.com/sixtenbe/1178136
[findpeaks_of_ref]: http://octave.sourceforge.net/signal/function/findpeaks.html
[oct2py]: https://github.com/blink1073/oct2py
[overview_github]: https://github.com/MonsieurV/py-findpeaks
[PeakUtils]: https://bitbucket.org/lucashnegri/peakutils
[indexes]: http://pythonhosted.org/PeakUtils/reference.html#peakutils.peak.indexes
[jupyter notebook]: http://nbviewer.ipython.org/github/demotu/BMC/blob/master/notebooks/DetectPeaks.ipynb
[hn_md_comment]: https://news.ycombinator.com/item?id=10524933
[interpolate_ref]: http://pythonhosted.org/PeakUtils/reference.html#peakutils.peak.interpolate
[baseline_ref]: http://pythonhosted.org/PeakUtils/reference.html#peakutils.baseline.baseline
[baseline_example]: http://pythonhosted.org/PeakUtils/tutorial_a.html#estimating-and-removing-the-baseline
[highpass_filter]: http://www.nws.noaa.gov/os/csd/pds/PCU2/statistics/Stats/part2/Filter_HP.htm
[cb_samples]: https://github.com/MonsieurV/py-findpeaks/blob/b7882a50aabed3044411b849119dda1696dbd0c8/tests/vector.py#L8
