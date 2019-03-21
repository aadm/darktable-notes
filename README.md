# darktable-notes

A collection of tips, tricks and recipes for Darktable. Inspired by [this thread on pixls.us](https://discuss.pixls.us/t/darktable-tricks/7903).


## noise reduction

Following the recipe suggested in [DT's manual](https://www.darktable.org/usermanual/en/correction_group.html#denoise_profiled);
the first instance reduce chroma (color) noise, the second luma (lightness) noise.

* denoise (profiled), mode=wavelets, strength=1.0, blend mode=color or HSV color, opacity=100%
* denoise (profiled), mode=non-local means, patch-size=4, strength=4, blend mode=lightness or HSV Lightness, opacity=40%

Recipe from discuss.pixls.us:

* denoise (profiled), mode=wavelets, strength=1.0, blend mode=color, opacity=100%
* denoise (profiled), mode=wavelets, strength=1.0, blend mode=lightness, opacity=50%
* denoise (profiled), mode=non-local means, patch-size=4, strength=1, blend mode=averaging, opacity=30%


Recipe from discuss.pixls.us, tested for high-ISO m4/3 images:

* hot pixels, threshold=0.01, strength=0.7
* demosaic method=amaze. color smoothing=five times, match greens=disabled
* denoise (profiled), mode=wavelets, strength=0.25, blend mode=color, parametric mask on L channel 0 0 0 15
* denoise (profiled), mode=non-local means, patch-size=4 strength=1, blend mode=averege, parametric mask on L channel 0 0 0 15

Filtering (after Harry Durgin's youtube tutorials?):

* Lowpass 1, Radius=15, Gaussian, Contrast=0.93, Brightness=0.03, Saturation=1.0, Blend=uniformly, Blend Mode=Lab a-channel, Opacity=100%

* Lowpass 2, Radius=15, Gaussian, Contrast=0.93, Brightness=0.03, Saturation=1.0, Blend=uniformly, Blend Mode=Lab b-channel, Opacity=100%

Using Equalizer module (probably taken from <https://tomassobekphotography.co.nz/articles/reducing-noise-in-Darktable.php>):

* Equalizer - stock preset ‘denoise luma only,’ mix 1.0
* reduce chroma noise at low ISO use equalizer (less aggressive than default); moderate to high ISO use profiled denoise (wavelet mode, blend-mode color) or bilateral filter denoising or the low pass filter

Using Denoise module:

* Denoise (non-local means): starting point=1/10th ISO luma, 2x chroma (e.g, if ISO 160 --> luma 16% chroma 32%)

Another recipe:

* high ISO: bilateral denoise with radius set to the maximum, red and blue to the maximum (or close) and green to the minimum. The red and blue channels are frequently more noisy than the green one, and using this parameters allow to smooth them without losing too much details.
Please note that this technique is inefficient if any denoising smooths the color before this module in the pipeline. For example, if you use denoise profile with wavelet in “color” mode, this technique will not give proper result.
Thus, when I use it, I smooth color noise later in the pipeline, with a second bilateral denoise module in “color” mode



