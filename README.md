# darktable-notes

A collection of tips, tricks and recipes for Darktable. Inspired by [this thread on pixls.us](https://discuss.pixls.us/t/darktable-tricks/7903).

## My doubts about Darktable's workflow

rgbcurve vs LAB tone curve; is working with tone curve in LAB mode deprecated? Is tone curve in RGB mode the same as RGB curve module?

proper workflow is: base curve + tone curve / rgb curve (the easy way) or filmic rgb+tone equalizer. This second way would avoid the user to use shadow/highlights, tone / rgb curves altogether correct?

working with tone/rgb curve or filmic rgb I've gotten used to "fix" the oversaturation issues (that I was happily leaving it in place in Lightroom I remember and thought that this was a natural and desired consequence, until DT's develeoped told us "careful! this increase in saturation is a by-product, is something conceptually 'wrong') but then how do I go adding back some saturation? Is velvia a good module to use (to me it seems like a module that adds the saturation in all the correct places; for instance vibrance I'm not sure what to make of it, while I distinctly rememebr that in LR this was the slider to use as opposed to saturation; in DT it seems to me like vibrance doesn't do much)? Is the saturation slider in the new color balance module the one to use?

correcting color casts etc; I was used to work in tone curve in LAB mode and adjust the A/B curves but how do I do this if I'm not using tone curves altogether?

sequence:

* white balance -> filmic rgb -> exposure -> color corrections

After exposure:
checked using the global color picker Lab readings a


 pixel has a luminance higher than 100 %
no pixel has a luminance lower than 0 % (that is not always possible)
no pixel on the subject has a luminance lower than 16-18 % and higher than 92-96 % (excepted deliberate use of low-key or high-key)
white skins have an average luminance of 65-85 %
black skins have an average luminance of 25-45 %
tanned skins have an average luminance of 45-65 %
non-human subjects have an average luminance around 50 % (excepted deliberate use of low-key or high-key)

## notes from the forum

The purple flare with grid artifacts
------------------------------------

https://discuss.pixls.us/t/pattern-in-lens-flare/14062

Moire-removal routines might help with this sort of grid artifact. In dt I use Denoise Bilateral Filter: maximize all the values (radius and RGB channels) and apply the Color blending mode. If it affects other colours in the frame you could apply this with a drawn mask. Then I’d change the demosaicing to FDC and apply a Denoise Profiled luma preset to get something like this:


Misc
----

test this:
https://discuss.pixls.us/t/darktable-opencl-optimizations-for-medium-high-dpi-displays-for-interactive-image-editing/11602/10


color balance (when possible)
-----------------------------

exposure. Center the histogram (set as linear) visually keeping room on both side to avoid clipping (the automatic clipping fails some times).
unbreak. Idem. Keeping grey at 18%, play with black point and dynamic range against histogram still keeping room on both side to avoid clipping. Considering only color picker may be misleading. It happens to have max Lab at 96 and still saturation zones on the image. The histogram is more reliable (and faster).
lookup table with camera profile preset (optional).
color balance (slope offset power). Center the histogram with slope, enlarge it with contrast until before clipping, and balance the image with power. Then tweaks with contrast and SOP if necessary.
No unexpected or weird effect as far I can see. A beautiful (well, … depends ! :wink:) and natural looking image.
Bravo !!!


Yes, I use the exposure module with the screen blending for my manual vignetting correction – I find it gives me better quality than the vignetting module.


The L channel/curve in the Tone Curve module is probably what you’re looking for. It works in the L*A*B* color space.

Use of the Luma tab of the Equalizer module or the Lightness tab of the Color Zones module will also yield pleasing results, depending on what exactly you want to do.


(https://discuss.pixls.us/t/do-you-know-if-darktable-has-a-lightness-control-tool/7223/2)




Forget about highlights. Tone equalizer knows no highlights, it just splits the dynamic range you feed it in 9 zones, spaced by 1 EV. Highlights may fall into the 0, -1, -2 EV band, that doesn’t change anything. Always try to spread the histogram over all the available nodes, so you get maximum control over your whole dynamic range.

Preliminary answer here, for @Julien76, @gadolf, @asn and @phweyland, regarding the masking:

The mask used in tone equalizer simply compresses an RGB image into a grey map representing the pixel lightness, using several norms, and is used internally to map the pixel lightness to the control nodes to apply the corresponding exposure correction.
If you don’t use the details preservation option, the tone equalizer falls back to a simple tone curve with built-in chroma preservation. That’s good if you want to increase the contrast in a picture and don’t mind the over-sharpening (aka you want local and global contrast increase).
If you are decreasing the contrast (in HDR situations) or want to increase the global contrast without affecting the local one (in studio portraits, to avoid making skin flaws pop out too much), you need to post-process the mask and use the details preservation mode.
To avoid affecting the local contrast along, we need to make sure that the same exposure correction is applied over contiguous areas of the picture (say : all the background gets -1 EV, and all the foreground gets +1 EV) and these areas are properly separated.

We therefore need to post-process the mask with a surface blur. We do that with a guided filter.

chose the luminance estimator that gives you the most separation between the areas you want to dodge, and the ones you want to burn,
chose the smoothing diameter depending on the size of the elements you want to isolate,
adjust the edge feathering:
if you get halos along sharp edges, increase it,
if you get too much local contrast compression, decrease it.
Note that a very high edge feathering (> 100) is equivalent to disabling the details preservation at all.
if the trade-off beween smoothing diameter and edge feathering is still not enough to get a good edge-aware blurring:
increase the mask diffusion (aka apply the guided filter iteratively on top of itself),
increase the mask quantization (aka use a pseudo-boolean mask in the guided filter).
BUT…

The core of the guided filter is actually a box average (also called box blur), which computes the local average in a square box around each pixel. As you increase the mask diffusion, the mask quantization, and decrease the edge feathering, the post-processed mask will tend to its average lightness. Thus the histogram will shrink around its average, and most nodes in the equalizer will consequently control nothing.

So the mask exposure and contrast compensations are just there to counter the averaging effect of the guided filter and force-spread the histogram over all available nodes.

That’s why we don’t care about highlights or shadows in this module, because what the -1EV band actually represents depends on the masking options, and doesn’t change anything since you have the interactive correspondance between image preview and EV band.

At any rate, preview the mask for each step, and read the tooltips labels I have put on each slider, that should help understanding what’s going on. Litterally every setting has a toolti


(https://discuss.pixls.us/t/darktable-tone-equalizer-help-wanted/14549/7)



to lose time tweaking them, just:

click twice on the colour picker of the mask exposure compensation (once to reset the value, then once to automatically center the histogram on the -4 EV channel)
click twice on the colour picker of the mask contrast compensation (once to reset the value, then once to automatically spread the first and last deciles of the histogram between -7EV and - 1 EV)
display the exposure mask to ensure you get a proper mask (not all black, but not all white). The mask display is there to help you understand what’s going on inside.

(https://discuss.pixls.us/t/tone-equalizer-question/14726/12)



https://discuss.pixls.us/t/on-the-search-for-a-good-dt-workflow/5005
https://discuss.pixls.us/t/introducing-a-new-color-balance-mode-in-darktable/9142
https://discuss.pixls.us/t/got-an-image-problem-go-see-the-image-doctor/14518
https://github.com/darktable-org/darktable/pull/2905
https://github.com/darktable-org/darktable/pull/3075


## Workflow

- exposure
- white balance
- color profiles / LUT
- filmic (tones)
- color balance (colors)
- denoising and enhancements as you please.

(maybe exposure after filmic)

## FILMICRGB

## TONE EQUALIZER

masking / luminance estimator: RGB Euclidean Norm
## EXPOSURE

Guidelines for severely underexposed, high-ISO images; may be especially useful for older or small sensors (e.g. APS-C sensor in Nikon D7000 or Fuji cameras at >=3200 ISO). To avoid fake bluish/purple tint of very digital noise in very dark portions of the frame increase the blacks slider of a tiny percentage to effectively "crush the blacks" and avoid resorting to complicated techniques to deal with noise in those areas.

* _exposure_, strength according to taste, black=slightly positive values (e.g., 0.0020 / 0.0040)

Dramatic sky, from [@s7habo's Editing Moments Episode #3](https://www.youtube.com/watch?v=8Rhd5UtpYW4):

* *channel mixer*, destination=gray, red=(increase) blue=(decrease), blend=drawn mask with a gradient matching the sky, blend mode=HSV ligthness
* _haze removal_, decrease opacity

## SHARPENING

* _sharpen_, radius=0.7, amount=1.5, threshold=1, blend=parametric, blend mode=lab lightness, opacity=100%; Parametric mask: L, input upper triangle left+10, right-10, mask refinement feath radius 0, mask blur 2.

* _sharpen_, radius=0.9, amount=1.2, threshold=0.5, blend=parametric, blend mode=lab lightness, opacity=100%; Parametric mask: L, input 0-15-85-100, mask refinement feath radius 0, mask blur 2.

check also: https://www.dpreview.com/forums/post/60562600?image=0


## structure/clarity

* _local contrast_, mode=bilateral grid, detail=160%, coarseness=15, contrast=60, blend=parametric, mode=normal, opacity=100%; Parametric mask: L, input 0-88-100-100.

Clarity:

* _local contrast_, mode=local laplacian filter, detail=150%, highlights=90%, shadows=100%, midtone range=0.2, blend=parametric, blend mode=Lab lightness, opacity=100%; Parametric mask: L, output=0-10-90-100, input 0-0-100-100.

Structure enhancement:

* _highpass_, sharpness=20%, contrast boost=10%, blend=parametric, blend mode=softlight, opacity=100%; Parametric mask: L, input 0-20-90-100.

## NOISE REDUCTION

The official recipe suggested in [DT's manual](https://darktable.gitlab.io/doc/en/correction_group.html#denoise_profiled) (parameters to be further customized according to user choice); the first instance reduce chroma (color) noise, the second luma (lightness) noise. Wavelet is particularly good for removing color noise but can be also used for luma (lightness) noise.

* _denoise (profiled)_ 1st instance, mode=wavelets, strength=1.0, blend mode=color or HSV color, opacity=100%
* _denoise (profiled)_ 2nd instance, mode=non-local means, patch-size=4, strength=4, blend mode=lightness or HSV Lightness, opacity=40%

This is largely superceded by the new algorithm (DT 3.0), but before discussing that, keep in mind that:

> there is no such thing as chroma or luma or colour noise. It’s just noise. How it looks, once it enters our perceptual system, doesn’t change what it is: garbled data on RGB channels.

> Similarly, the denoise profile module in darktable doesn’t know any colour or luminance since it operates in camera RGB. That camera RGB has nothing to do with our perceptual system, and will need to meet a colour profile to be remapped to XYZ and then to well-behaved RGB spaces, where chrominance and luminance have a meaning.

> Blending the denoise profile in lightness or colour is therefore a theoretical mistake since these are Lab modes. HSV lightness or colour are equally wrong since the conversion RGB -> HSV assumes well-behaved RGB spaces, while the camera RGB can be absolutely whatever.

> The last changes in denoise profile module (in future darktable 3.0) should make these blending modes irrelevant, since they essentially hide misbehaviours of the denoising algo

(by @aurelienpierre, written [here](https://discuss.pixls.us/t/call-to-advice-for-fighting-color-noise/3366/25))

The *new* way as described in <https://discuss.pixls.us/t/changes-in-noise-reduction-for-darktable-2-7-3-0/14672>

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