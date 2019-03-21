# darktable-notes

A collection of tips, tricks and recipes for Darktable. Inspired by [this thread on pixls.us](https://discuss.pixls.us/t/darktable-tricks/7903).

## EXPOSURE

Guidelines for severely underexposed, high-ISO images; may be especially useful for older or small sensors (e.g. APS-C sensor in Nikon D7000 or Fuji cameras at >=3200 ISO). To avoid fake bluish/purple tint of very digital noise in very dark portions of the frame increase the blacks slider of a tiny percentage to effectively "crush the blacks" and avoid resorting to complicated techniques to deal with noise in those areas.

* exposure, strength according to taste, black=slightly positive values (e.g., 0.0020 / 0.0040)

## SHARPENING

* sharpen, radius=0.7, amount=1.5, threshold=1, blend parametric, mode=lab lightness, opacity=100%; Parametric mask: L, input upper triangle left+10, right-10, mask refinement feath radius 0, mask blur 2.

## NOISE REDUCTION

The official recipe suggested in [DT's manual](https://www.darktable.org/usermanual/en/correction_group.html#denoise_profiled) (parameters to be further customized according to user choice); the first instance reduce chroma (color) noise, the second luma (lightness) noise.

* denoise (profiled), mode=wavelets, strength=1.0, blend mode=color or HSV color, opacity=100%
* denoise (profiled), mode=non-local means, patch-size=4, strength=4, blend mode=lightness or HSV Lightness, opacity=40%

The ["denoise triple" recipe by @asn](https://discuss.pixls.us/t/darktable-tricks/7903/6)

* denoise (profiled), mode=wavelets, strength=1.0, blend mode=color, opacity=100%
* denoise (profiled), mode=wavelets, strength=1.0, blend mode=lightness, opacity=50%
* denoise (profiled), mode=non-local means, patch-size=4, strength=1, blend mode=averaging, opacity=30%

The ["m43 denoise" recipe by @Nor_man](https://discuss.pixls.us/t/darktable-tricks/7903/14), recommended for high-ISO micro 4/3 images:

* hot pixels, threshold=0.01, strength=0.7
* demosaic method=amaze. color smoothing=five times, match greens=disabled
* denoise (profiled), mode=wavelets, strength=0.25, blend mode=color, parametric mask on L channel 0 0 0 15
* denoise (profiled), mode=non-local means, patch-size=4 strength=1, blend mode=averege, parametric mask on L channel 0 0 0 15

The ["denoise gentle" recipe by @Kane_Davis](https://discuss.pixls.us/t/darktable-tricks/7903/8), increase/decrease Equalizer mix for fine tuning.

* Lowpass 1, Radius=15, Gaussian, Contrast=0.93, Brightness=0.03, Saturation=1.0, Blend=uniformly, Blend Mode=Lab a-channel, Opacity=100%
* Lowpass 2, Radius=15, Gaussian, Contrast=0.93, Brightness=0.03, Saturation=1.0, Blend=uniformly, Blend Mode=Lab b-channel, Opacity=100%
* Equalizer, stock preset 'denoise luma only', mix 1.0

The ["high-ISO" recipe by @rawfiner](https://discuss.pixls.us/t/darktable-tricks/7903/7); red and blue channels are frequently more noisy than the green one, this technique is inefficient if any denoising smooths the color before this module in the pipeline (e.g. if using denoise profile with wavelet in "color" mode). If needed, use a second instance of this module in "color" mode later in the pipeline.

* bilateral denoise, radius=maximum, red, blue=maximum (or close), green=minimum.

Guidelines on using Denoise module:

* Denoise (non-local means): starting point=1/10th ISO luma, 2x chroma (e.g, if ISO 160 --> luma 16% chroma 32%)

Other resources:
 * Tomas Sobek's page: <https://tomassobekphotography.co.nz/articles/reducing-noise-in-Darktable.php>
 * video tutorial by rawfiner: <https://youtu.be/RMbbLyKqdLY>

## SHADOWS AND HIGHLIGHTS / DODGING AND BURNING

In combination or as an alternative to shadow/highlights, use the following recipe to raise shadows for example without getting the halos common in the Shadow/Highlight module.

* Exposure module, +1 EV, parametric mask on L channel to apply only in darker areas o lighter areas.
