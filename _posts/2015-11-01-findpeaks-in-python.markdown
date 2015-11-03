---
title: "Peak detection in the Python world"
categories: [Digital signal processing]
tags: [DSP, Python]
brief: "An overview of the peak detection algorithms in Python."
draft: true
---

As I was working on a signal processing project for [Equisense][], I've come to need an equivalent
of the MatLab [`findpeaks` function][findpeaks_ref] in the Python world.

For those not familiar to digital signal processing, peak detection is as easy to understand as it sounds: this is the process of finding peaks - we also names them local maxima or local minima - in a signal. The MatLab DSP Toolbox makes this super easy with its `findpeaks` function. Saying your want to search local maxima in an audio signal, for example 2000 samples of the Laurent Garnier famous track Cripsy Bacon, all you have to do is:

```cb = audioread('Crispy_Bacon.wav');
findpeaks(cb(50061:52060), 'MinPeakDistance', 100, 'MinPeakHeight', 0.04)
```

![Plot of MatLab findpeaks](/img/2015-11-01-findpeaks-in-python/matlab_findpeaks.png)

You can see we can specify filtering options to the function so we can discard what is not interesting for us. This is great, but I needed something similar working in Python. In a perfect world, I wanted a function that gives exactly the same results provided the same signal, so we have consistency between our Python processing code and the MatLab code.

Contrary to other MatLab functions that have direct or very near direct equivalents in the Numpy and Scipy scientific and processing packages, it is no easy task to get the same results from the Scipy [`find_peaks_cwt` function][find_peaks_cwt_ref] that from the MatLab `findpeaks`. Even worse, the wavelet convolution approach of `find_peaks_cwt` is not straightforward to work with: it adds complexity that is of no use for well-filtered and noiseless signals.

```import numpy as np
from scipy.signal import find_peaks_cwt
cb = np.array([-0.010223, ... ])
indexes = find_peaks_cwt(cb, np.arange(1, 550))
```

![Plot of find_peaks_cwt_ref](/img/2015-11-01-findpeaks-in-python/find_peaks_cwt.png)

Wondering how to make our algorithms works as simply with Python that they were in MatLab, I've search around the web for other peak detection algorithms available in Python. [Stackoverflow][so_1713335] get me to [`peakdetect`][peakdetect], a translation of a MatLab script. As it is clearly more trivial to use that `find_peaks_cwt`, it still won't give you the same results that the MatLab `findpeaks` function. The algorithm don't find all peaks on low sampled signals or on short samples, and don't have either a support for minimum peak height filter.

```import numpy as np
from peakdetect import peakdetect
cb = np.array([-0.010223, ... ])
peaks = peakdetect(cb, lookahead=100)
```

![Plot of peakdetect](/img/2015-11-01-findpeaks-in-python/peakdetect.png)

Going ahead I've checkout the GNU Octave project, a processing-intended language quite similar to MatLab. The Octave-Forge repository hosts a digital signal processing package with a [`findpeaks` function][findpeaks_of_ref]. Bingo? Well, yes and no. The function have an appealing interface, with a great filtering support. But this is not a Python project: as you'll find ways to call your Octave distribution from your Python code (see [oct2py][]), it surely won't be effective at large scale and makes the requirements for your code more complex.

```import numpy as np
from oct2py import octave
cb = np.array([-0.010223, ... ])
octave.eval("pkg load signal")
(peaks, indexes) = octave.findpeaks(cb, 'DoubleSided',
    'MinPeakHeight', 0.04, 'MinPeakDistance', 100, 'MinPeakWidth', 0)
```

![Plot of Octave-Forge findpeaks](/img/2015-11-01-findpeaks-in-python/octave_findpeaks.png)

As I was going to code a Python adaptation of the Octave-Force `findpeaks`, I finally found what I was searching: a Python native equivalent of the MatLab `findpeaks`, with minimum distance and height filtering support. I even found two!

The first is [the PeakUtils package][PeakUtils] by Lucas Hermann Negri which provides 1D peak detection utilities. Its [`indexes`][indexes] function allows you to detect peaks with minimum height (`thres` param) and distance (`min_dist` param) filtering.

```import numpy as np
import peakutils.peak
cb = np.array([-0.010223, ... ])
indexes = peakutils.peak.indexes(cb,
    thres=0.02/max(cb), min_dist=100)
```

![Plot of PeakUtils indexes](/img/2015-11-01-findpeaks-in-python/peakutils_indexes.png)

The second is published on a [jupyter notebook][] and is written by Marcos Duarte. Not easy to find, but the greatest so far for what I need: the parameters for filtering are modeled after the MatLab `findpeaks` ones. It comes as a single source file and only depends on Numpy, so it is no big deal to integrate. And more importantly, it will consistently get you the same results than MalLab `findpeaks`!

```import numpy as np
from detect_peaks import detect_peaks
cb = np.array([-0.010223, ... ])
indexes = detect_peaks(cb, mph=0.04, mpd=100)
```

![Plot of Marcos Duarte detect_peaks](/img/2015-11-01-findpeaks-in-python/detect_peaks.png)

To avoid others the same roaming I've put on GitHub [an overview][overview_github] of these findings. Let me know if you got another open-source alternatives so we update the list.

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