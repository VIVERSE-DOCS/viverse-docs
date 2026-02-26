---
description: Learn how add Post Effects to your VIVERSE Worlds, and explore their settings
---

# Post Effects

***

## About

PlayCanvas Toolkit offers a rich collecion of Post Effects, allowing creators to customize look and feel of their Worlds without touching any shader code! Below is a list of Post Effect you can add to your experience:

* Overlays and vignettes
* Bloom, Depth of Field (Bokeh) and Tilt Shift
* Hue / Saturation and Brightness / Contrast
* Sepia and Luminosity (Black and White)
* Screen Space Ambient Occlusion

## Usage

The Post Effects section can be found under <img src="../../.gitbook/assets/viverse-icon.png" alt="" data-size="line"> VIVERSE Menu, which is located in the left sidebar. You can stack multiple effects on top of each other, but please be mindful of possible performance impact! Here is an example of adding two effects to your Scene — Sepia and Vignette:

{% columns %}
{% column width="66.66666666666666%" %}
{% stepper %}
{% step %}
### Effect 01: Sepia

* Navigate to Post Effects section and click **Edit**, then **Add Post Effect**
* Click on **Type** selector and find `Sepia`
* You'll see bright yellow prompt informing you about unsaved changes. Click **Save** button to save your current Post Effect configuration
* Launch your Scene — you should see it in Sepia tone now!
{% endstep %}

{% step %}
### Effect 02: Vignette

* Go through the same steps and add another **Post Effect** on top your current one, but this time choose `Vignette` at the end of the list
* As previously, save your changes and launch your Scene. Now you have both Sepia and Vignette applied!
* Try adding more if you like! You can have as much effects as you like, as long as rendering performance is not hindered
{% endstep %}
{% endstepper %}
{% endcolumn %}

{% column width="33.33333333333334%" %}
<figure><img src="../../.gitbook/assets/pe01.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/pe03.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/pe05.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/pe06.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

## Reference

***

{% columns %}
{% column width="25%" %}
`Blend` \
`Effect`
{% endcolumn %}

{% column width="50%" %}
Adds a fullscreen transparent overlay on top of your experience. Typically used for vignettes, dirty camera effects, and so on
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/pe1a (1).png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`Bloom` \
`Effect`
{% endcolumn %}

{% column width="50%" %}
Adds soft fringe of light around bright areas in your world, simulating imaging artefacts of real world cameras
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/pe2a.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`Bokeh` \
`Effect`
{% endcolumn %}

{% column width="50%" %}
Adds Depth of Field effect, blurring out of focus areas, similar to how cinematic cameras behave in the real world
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/pe3a.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`Brighness` \
`Contrast`\
`Effect`
{% endcolumn %}

{% column width="50%" %}
Adjusts Brightness and Contrast of your experience, similar to Photoshop image adjustment with the same name
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/pe4a.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`Edge` \
`Detect`\
`Effect`
{% endcolumn %}

{% column width="50%" %}
Extracts edges from rendered frame and draws them on top of your experience in an overlay
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/pe5a.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`Fxaa` \
`Effect`
{% endcolumn %}

{% column width="50%" %}
Applies Fast Approximate Anti-Aliasing to your rendering pipeline. Particularly useful when your world has a lot of alpha blending and per-pixel shader effects
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/pe6a.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`Horizontal` \
`TiltShift`\
`Effect`
{% endcolumn %}

{% column width="50%" %}
Adds subtle Horizontal Tilt Shift to Depth of Field effect, blurring out left and right portions of the screen
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/pe7a.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`Hue`\
`Saturation`\
`Effect`
{% endcolumn %}

{% column width="50%" %}
Adjusts Hue and Saturation of your experience, similar to Photoshop image adjustment with the same name
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/pe8a.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`Luminosity`\
`Effect`
{% endcolumn %}

{% column width="50%" %}
Renders your experience in black and white mode, based on per-pixel luminosity values
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/pe9a.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`Sepia`\
`Effect`
{% endcolumn %}

{% column width="50%" %}
Applies Sepia filter to your experience, similar to Photoshop filter with the same name
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/pe10a.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`SSAO`\
`Effect`
{% endcolumn %}

{% column width="50%" %}
Adds Screen-Space Ambient Occlusion to your scene. This is a computationally expensive effect, so consider using it in desktop-only experiences
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/pe11a.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`Vertical`\
`TiltShift`\
`Effect`
{% endcolumn %}

{% column width="50%" %}
Adds subtle Vertical Tilt Shift to Depth of Field effect, blurring out top and bottom portions of the screen
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/pe12a.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`Vignette`\
`Effect`
{% endcolumn %}

{% column width="50%" %}
Adds subtle darkening to screen corners, simulating real world camera lenses
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/pe13a.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***
