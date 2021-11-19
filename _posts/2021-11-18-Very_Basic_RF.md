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

* amplitude = sqrt(I<sup>2</sup> + Q<sup>2</sup>)
* power = I<sup>2</sup> + Q<sup>2</sup>

In DSP, often amplitude == power == 1.

## Amplitude, Power & Decibels

dB = 10 * log<sub>10</sub>(P<sub>1</sub>/P<sub>2</sub>) = 10 * log<sub>10</sub>((V<sub>1</sub>/V<sub>2</sub>)<sup>2</sup>) = 20 * log<sub>10</sub>(V<sub>1</sub>/V<sub>2</sub>).

Most of the time we deal with power, so dB = 10 * log<sub>10</sub>(P<sub>1</sub>/P<sub>2</sub>).

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

Variance = (Standard Deviation)<sup>2</sup> = σ<sup>2</sup>

For AWGN power is normally measured as total noise power in a given bandwidth:

Noise Power = Noise Spectral Density * Bandwidth

Simulation:

```python
n = 1024
signal_power = 1.0 # dBm
snr_db = 10 
snr_linear = 10.0**(snr_db/10.0)
noise_power = variance = signal_power / snr_linear # 0.1 dBm
std_deviation = np.sqrt(noise_power)
noise = 1.0/np.sqrt(2) * (np.random.randn(n) + 1j*np.random.randn(n)) * std_deviation
signal = qpsk(n) * np.sqrt(signal_power)
channel_coefficient = 1
received_signal = channel_coefficient * signal + noise
print(np.var(signal))  # signal_power -> 1.0 dBm
print(np.var(noise))   # noise_power -> 0.1 dBm
```

## Sampling

How discrete samping works:

![sampling.png]({{ site.github.url }}/res/2021-11-18-Very_Basic_RF/sampling.png#middle#middle)

Nyquist Samping Rate:

![nyquist_sampling.png]({{ site.github.url }}/res/2021-11-18-Very_Basic_RF/nyquist_sampling.png#middle#middle)

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

## FFT Scaling:

[https://electronics.stackexchange.com/a/25941](https://electronics.stackexchange.com/a/25941):
>The 1/N scaling factor is almost arbitrary placed. An unscaled FFT followed by an unscaled IFFT using exactly the same complex exponential twiddle factors multiplies the input vector by scaler N. In order to get back the original waveform after an IFFT(FFT())round trip (thus making them inverse functions), some FFT/IFFT implementation pairs scale the FFT by 1/N, some scale the IFFT by 1/N, some scale both by 1/sqrt(N).

[numpy's doc](https://numpy.org/doc/stable/reference/routines.fft.html#normalization):
> The default normalization ("backward") has the direct (forward) transforms unscaled and the inverse (backward) transforms scaled by 1/n. It is possible to obtain unitary transforms by setting the keyword argument norm to "ortho" so that both direct and inverse transforms are scaled by 1/sqrt(n).

Parseval's theorem: scale both FFT by `1/sqrt(n)` and IFFT by `sqrt(n)`.

## FFT shift:

![fft-shift.svg]({{ site.github.url }}/res/2021-11-18-Very_Basic_RF/fft-shift.svg#middle#middle)

[https://pysdr.org/content/frequency_domain.html#fast-fourier-transform-fft](https://pysdr.org/content/frequency_domain.html#fast-fourier-transform-fft):
>It is always the case; the output of the FFT will always show -f<sub>s</sub>/2 to f<sub>s</sub>/2 where f<sub>s</sub> is the sample rate. I.e., the output will always have a negative portion and positive portion. If the input is complex, the negative and positive portions will be different, but if it real then they will be identical.
>
>Regarding the frequency interval, each bin corresponds to f<sub>s</sub>/N Hz, i.e., feeding in more samples to each FFT will lead to more granular resolution in your output. A very minor detail that can be ignored if you are new: mathematically, the very last index does not correspond to exactly f<sub>s</sub>/2, rather it’s f<sub>s</sub>/2 - f<sub>s</sub>/N which for a large N will be approximately f<sub>s</sub>/2.




## Power Spectral Density


## References

[https://en.wikipedia.org/wiki/Fourier_transform](https://en.wikipedia.org/wiki/Fourier_transform)

[https://en.wikipedia.org/wiki/Discrete_Fourier_transform](https://en.wikipedia.org/wiki/Discrete_Fourier_transform)

[https://pysdr.org/](https://pysdr.org/)

[https://www.hebergementwebs.com/signals-and-systems-tutorial/signal-sampling-theorem](https://www.hebergementwebs.com/signals-and-systems-tutorial/signal-sampling-theorem)

[http://paulbourke.net/miscellaneous/dft/](http://paulbourke.net/miscellaneous/dft/)

[https://dsp.stackexchange.com/questions/33849/adding-awgn-noise-with-a-correct-noise-power-to-the-signal](https://dsp.stackexchange.com/questions/33849/adding-awgn-noise-with-a-correct-noise-power-to-the-signal)