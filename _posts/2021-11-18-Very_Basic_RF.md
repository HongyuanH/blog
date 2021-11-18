---
layout:            post
title:             "Very Basic RF"
date:              2021-11-18
tags:              RF
category:          Others
author:            hongyuan

---

[This textbook](https://pysdr.org/) is awesome! Here I am just putting the very basics for myself to 复习 from time to time.

## IQ signal

For IQ signel:

* amplitude = sqrt(I^2 + Q^2)
* power = I^2 + Q^2

In DSP, often amplitude == power == 1.

## Amplitude, Power & Decibels

For amplitude: dB = 20 * log10(v1/v2).

For power: dB = 10 * log10(p1/p2).

Most of the time we deal with power, so dB = 10 * log10(p1/p2).

In DSP, power usually gets normalized and has no units.

Conversion between dB and linear, or between dBm and mWatt:

```python
def linear_to_db(linear):
    db = 10.0 * np.log10(linear)
    return db

def db_to_linear(db):
    linear = 10.0 ** (db / 10.0)
    return linear

def dbm_to_mwatt(dbm):
    mwatt = 10.0 ** (dbm / 10.0)
    return mwatt

def mwatt_to_dbm(mwatt):
    dbm = 10.0 * np.log10(mwatt)
    return dbm
```

## Noise & SNR

## Fourier Transform

Continuous fourier transform:

![fourier_transform_1.svg]({{ site.github.url }}/res/2021-11-18-Very_Basic_RF/fourier_transform_1.svg#middle#middle)

Continuous inverse fourier transform:

![fourier_transform_2.svg]({{ site.github.url }}/res/2021-11-18-Very_Basic_RF/fourier_transform_2.svg#middle#middle)

Discrete fourier transform (DFT):

![fourier_transform_3.svg]({{ site.github.url }}/res/2021-11-18-Very_Basic_RF/fourier_transform_3.svg#middle#middle)

Relationship between frquency domain and time domain of a discrete signal can be illustrated as:

![dft.gif]({{ site.github.url }}/res/2021-11-18-Very_Basic_RF/dft.gif#middle#middle)

Linear property:

![linear_property.svg]({{ site.github.url }}/res/2021-11-18-Very_Basic_RF/linear_property.svg#middle#middle)

Shift property:

![shift_property.svg]({{ site.github.url }}/res/2021-11-18-Very_Basic_RF/shift_property.svg#middle#middle)

Scaling property:

![scaling_property.svg]({{ site.github.url }}/res/2021-11-18-Very_Basic_RF/scaling_property.svg#middle#middle)

Convolution property:

![conv_property.svg]({{ site.github.url }}/res/2021-11-18-Very_Basic_RF/conv_property.svg#middle#middle)

## FFT

Scaling:

Shift:



## Power Spectral Density

## Bandwidth


## References

[https://en.wikipedia.org/wiki/Fourier_transform](https://en.wikipedia.org/wiki/Fourier_transform)

[https://en.wikipedia.org/wiki/Discrete_Fourier_transform](https://en.wikipedia.org/wiki/Discrete_Fourier_transform)

[https://pysdr.org/](https://pysdr.org/)

[http://paulbourke.net/miscellaneous/dft/](http://paulbourke.net/miscellaneous/dft/)

[https://dsp.stackexchange.com/questions/19615/converting-raw-i-q-to-db](https://dsp.stackexchange.com/questions/19615/converting-raw-i-q-to-db)

Note on bandwidth you probably don't need to read
It may also be relevant to note that if you filter a signal, you are by definition removing some of the signal power, so the measurement will be smaller.

In particular, a FFT (such as is the primary visual display in tools like SDR#) can loosely be thought of as a large collection of extremely sharp filters; each output “bin” collects some fraction of the input power. Accordingly, the power in each bin depends on the width of the bin. If you divide by the width of the bin in hertz (that value being sample rate/FFT length) before taking the logarithm, then instead of dB power, you measure dB power spectral density, which has the advantage of being independent of the FFT bin width if the features you care about are wider than one bin (e.g. a wideband modulated signal); if they are narrower (e.g. pure tones) then the power value is more useful.

[https://dsp.stackexchange.com/questions/33849/adding-awgn-noise-with-a-correct-noise-power-to-the-signal](https://dsp.stackexchange.com/questions/33849/adding-awgn-noise-with-a-correct-noise-power-to-the-signal)