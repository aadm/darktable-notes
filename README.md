# darktable-notes

A collection of tips, tricks and recipes for Darktable. Inspired by [this thread on pixls.us](https://discuss.pixls.us/t/darktable-tricks/7903).



## EXPOSURE

Guidelines for severely underexposed, high-ISO images; may be especially useful for older or small sensors (e.g. APS-C sensor in Nikon D7000 or Fuji cameras at >=3200 ISO). To avoid fake bluish/purple tint of very digital noise in very dark portions of the frame increase the blacks slider of a tiny percentage to effectively "crush the blacks" and avoid resorting to complicated techniques to deal with noise in those areas.

* _exposure_, strength according to taste, black=slightly positive values (e.g., 0.0020 / 0.0040)

Dramatic sky, from [@s7habo's Editing Moments Episode #3](https://www.youtube.com/watch?v=8Rhd5UtpYW4):

* *channel mixer*, destination=gray, red=(increase) blue=(decrease), blend=drawn mask with a gradient matching the sky, blend mode=HSV ligthness
* _haze removal_, decrease opacity

## SHARPENING

* _sharpen_, radius=0.7, amount=1.5, threshold=1, blend parametric, mode=lab lightness, opacity=100%; Parametric mask: L, input upper triangle left+10, right-10, mask refinement feath radius 0, mask blur 2.

check also: https://www.dpreview.com/forums/post/60562600?image=0


## structure/clarity

https://www.dpreview.com/forums/post/60562788?image=0

## NOISE REDUCTION

The official recipe suggested in [DT's manual](https://darktable.gitlab.io/doc/en/correction_group.html#denoise_profiled) (parameters to be further customized according to user choice); the first instance reduce chroma (color) noise, the second luma (lightness) noise. Wavelet is particularly good for removing color noise but can be also used for luma (lightness) noise.

* _denoise (profiled)_ 1st instance, mode=wavelets, strength=1.0, blend mode=color or HSV color, opacity=100%
* _denoise (profiled)_ 2nd instance, mode=non-local means, patch-size=4, strength=4, blend mode=lightness or HSV Lightness, opacity=40%

The ["denoise triple" recipe by @asn](https://discuss.pixls.us/t/darktable-tricks/7903/6)

* _denoise (profiled)_, mode=wavelets, strength=1.0, blend mode=color, opacity=100%
* _denoise (profiled)_, mode=wavelets, strength=1.0, blend mode=lightness, opacity=50%
* _denoise (profiled)_, mode=non-local means, patch-size=4, strength=1, blend mode=averaging, opacity=30%

The ["m43 denoise" recipe by @Nor_man](https://discuss.pixls.us/t/darktable-tricks/7903/14), recommended for high-ISO micro 4/3 images:

* _hot pixels_, threshold=0.01, strength=0.7
* _demosaic_ method=amaze. color smoothing=five times, match greens=disabled
* _denoise (profiled)_, mode=wavelets, strength=0.25, blend mode=color, parametric mask on L channel 0 0 0 15
* _denoise (profiled)_, mode=non-local means, patch-size=4 strength=1, blend mode=averege, parametric mask on L channel 0 0 0 15

The ["denoise gentle" recipe by @Kane_Davis](https://discuss.pixls.us/t/darktable-tricks/7903/8), increase/decrease Equalizer mix for fine tuning.

* _Lowpass_ 1, Radius=15, Gaussian, Contrast=0.93, Brightness=0.03, Saturation=1.0, Blend=uniformly, Blend Mode=Lab a-channel, Opacity=100%
* _Lowpass_ 2, Radius=15, Gaussian, Contrast=0.93, Brightness=0.03, Saturation=1.0, Blend=uniformly, Blend Mode=Lab b-channel, Opacity=100%
* _Equalizer_, stock preset 'denoise luma only', mix 1.0

The ["high-ISO" recipe by @rawfiner](https://discuss.pixls.us/t/darktable-tricks/7903/7); red and blue channels are frequently more noisy than the green one, this technique is inefficient if any denoising smooths the color before this module in the pipeline (e.g. if using denoise profile with wavelet in "color" mode). If needed, use a second instance of this module in "color" mode later in the pipeline.

* _bilateral denoise_, radius=maximum, red, blue=maximum (or close), green=minimum.

Guidelines on using Denoise module:

* Denoise (non-local means): starting point=1/10th ISO luma, 2x chroma (e.g, if ISO 160 --> luma 16% chroma 32%)

Other resources:

* Tomas Sobek's page: <https://tomassobekphotography.co.nz/articles/reducing-noise-in-Darktable.php>
* video tutorial by rawfiner: <https://youtu.be/RMbbLyKqdLY>

## SHADOWS AND HIGHLIGHTS / DODGING AND BURNING

In combination or as an alternative to shadow/highlights, use the following recipe to raise shadows for example without getting the halos common in the Shadow/Highlight module.

* Exposure module, +1 EV, parametric mask on L channel to apply only in darker areas o lighter areas.

## black and white

Using [color zones module](https://www.mail-archive.com/darktable-dev@lists.darktable.org/msg03788.html):

* _color zones_, saturation=0 (or use preset "black and white film"), lightness tab to choose which color should be light or dark.

## MASKS

@msd and @houz from [a discuss.pixls.us thread](https://discuss.pixls.us/t/sqsa-darktable-short-question-short-answer/5103):

* Luminosity mask on the output values instructs Darktable to select pixels with values
_after_ processing the image (e.g., increasing the exposure) will be in the specified range.
If raising the exposure by 0.7EV, some parts of the image have luminance still in the range specified for output.
But maybe rasing the exposure by 2.0EV the luminance will be much higher so these portions will be excluded from the mask.

* In almost all cases you want to use the input slider and leave the output slider alone.

## VIGNETTING AND OTHER EFFECTS

Vignetting according to @mica from [discuss.pixls.us](https://discuss.pixls.us/t/what-do-you-have-in-your-fav-list-and-why/12485/2):

* _vignette_, parametric mask on L channel with value=15%.

## MISC EFFECTS FROM CLAES

Effect: Soft Contrast

* Module _lowpass_, radius 269, soften with gaussian, contrast 0.01, brightness 0.22, saturation 0.34, Blend: uniform, Blend mode: softlight

Effect: Warm up Midtones

* Module _color balance_, mid-tones hue 58.45, mid-tones saturation 5.89

Effect: Cool down Midtones

* Module _color balance_, mid-tones hue & saturation as needed

Effect: Midtones contrast

* Module _contrast brightness saturation_, contrast as needed, Parametric mask input 0 50 50 100

Effect: Sharpen out-of-focus objects

* Module _highpass_, sharpness and contrast boost as needed, blend mode uniform, blend softlight

Effect: Mask irregular areas (better description needed)

* Modules _shadows and highlights_ and _tonecurve_, drawn & parametric mask, combine: exclusive, add path: draw a path outside the irregular area, Select L channel, Adjust markers

## batch scripts etc

To organize styles use hierarchical naming convention (supported from DT 2.6.2 for sure, not sure about earlier versions).

When downloading a bunch of styles, make sure to change the `<name>` tag to something that includes the `|` character to create categories.

For example, the Fuji styles downloadable from <https://blog.sowerby.me/fuji-film-simulation-profiles/> can be modified with the following bash script 

```bash
#!/bin/bash
OIFS="$IFS"
IFS=$'\n'
for file in `find . -type f -name "*.dtstyle"`
do
    echo "file = $file"
    sed 's/<name>Fuji XTrans III - /<name>Fuji XTrans III|/g' "$file">temp.dat
    mv temp.dat "$file"
done
IFS="$OIFS"
```