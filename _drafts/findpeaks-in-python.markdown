---
title: "Peak detection in the Python world"
categories: [Digital signal processing]
tags: [DSP, Python]
brief: "An overview of the peak detection algorithms in Python."
draft: true
---

As I was working on a signal processing project for [Equisense][], I've come to need an equivalent
of the MatLab [`findpeaks` function][findpeaks_ref] in the Python world.

![Plot of MatLab findpeaks](https://raw.githubusercontent.com/MonsieurV/py-findpeaks/master/images/matlab_findpeaks.png)

Contrary to other MatLab functions that have direct or very near equivalents in the Numpy or Scipy scientific and processing packages, it is no easy task to get same results from the Scipy [`find_peaks_cwt` function][find_peaks_cwt_ref] that from the MatLab `findpeaks`. Even worse, the wavelet convolution approach of `find_peaks_cwt` is not straightforward to work with. It adds complexity that is of no use for well-filtered and noiseless signals.

![Plot of find_peaks_cwt_ref](https://raw.githubusercontent.com/MonsieurV/py-findpeaks/master/images/scipy_find_peaks_cwt.png)

Wondering how to make our algorithms works as simply with Python that they were in MatLab, I've search around the web for other peak detection algorithms available in Python. [Stackoverflow][so_1713335] get me to [`peakdetect`][peakdetect], a translation of a MatLab script. As it is clearly more trivial to use that `find_peaks_cwt`, it still won't give you the same results that the MatLab `findpeaks` function. The algorithm don't find all peaks on low sampled signals or on short samples and don't have a support for minimum peak height filter.

![Plot of peakdetect](https://raw.githubusercontent.com/MonsieurV/py-findpeaks/master/images/sixtenbe_peakdetect.png)

Going ahead I've checkout the GNU Octave project, a processing-intended language quite similar to MatLab. The Octave-Forge repository hosts a digital signal processing package with a [`findpeaks` function][findpeaks_of_ref]. Bingo? Well, yes and no. The function have an appealing interface, with a great filtering support. But this is not a Python project: as you'll find ways to call your Octave distribution from your Python code (see [oct2py][]), it surely won't be effective at large scale and makes the requirements for your code more complex.

![Plot of Octave-Forge findpeaks](https://raw.githubusercontent.com/MonsieurV/py-findpeaks/master/images/octave_findpeaks.png)

As I was going to code a Python adaptation of the Octave-Force `findpeaks`, I finally found what I was searching: a Python native equivalent of the MatLab `findpeaks`, with minimum distance and height filtering support. I even found two!

The first is [a package][PeakUtils] by Lucas Hermann Negri that provides 1D peak detection utilities. Its [`indexes`][indexes] function allows you to detect peaks with minimum height (`thres` param) and distance (`min_dist` param) filtering.

![Plot of PeakUtils indexes](https://raw.githubusercontent.com/MonsieurV/py-findpeaks/master/images/peakutils_indexes.png)

The second is published on a [jupyter notebook][] and is written by by Marcos Duarte. Not easy to find, but the greatest for my need: the parameter interface for filtering is modeled after the MatLab `findpeaks`. It comes as a single source file and only depends on Numpy, so it is no big deal to integrate with your code. And more importantly it will consistently get you the same results than with MalLab `findpeaks`!

![Plot of Marcos Duarte detect_peaks](https://raw.githubusercontent.com/MonsieurV/py-findpeaks/master/images/detect_peaks.png)

To avoid others the same roaming I've put on GitHub [an overview][overview_github] of these findings. If you find another open-source alternatives, let me know so we update the list.

TODO: add illustration legend.

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
