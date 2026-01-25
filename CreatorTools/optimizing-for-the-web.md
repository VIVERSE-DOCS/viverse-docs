---
description: >-
  The page outlines the general optimization and design recommendations for all
  creators publishing on VIVERSE, regardless of which platform they are
  publishing from.
hidden: true
noIndex: true
---

# Optimizing for the Web

***

## Device Filtering on VIVERSE

Although any VIVERSE world can theoretically be accessed across all devices, the detail of the assets and rendering pipeline used may limit the ability of the world to run on lower powered devices. Creators building with VIVERSE may select which device(s) their world is capable of running on in the [World Settings](publishing-with-your-viverse-account/#world-settings) dashboard. If a user attempts to access the world on a device that is not included in the selected device filters, they will receive a notice that they cannot join on their existing device.

<figure><img src=".gitbook/assets/Screenshot 2025-06-08 at 10.36.40 AM.png" alt="" width="375"><figcaption><p>Device compatibility warning.</p></figcaption></figure>

## Performance Warnings

Regardless of the device filter selected by the creator, VIVERSE will warn users if the experience is not optimized for their device. These performance warnings are determined by based on the VRAM usage of the world with the following guidelines:

<table><thead><tr><th width="215.4302978515625">Performance Classification</th><th width="227.0836181640625">VRAM Usage</th><th>Recommend Device Filters</th></tr></thead><tbody><tr><td>Low</td><td>≤ 500 MB</td><td>PC / iOS / Android / HMD (VR Headsets)</td></tr><tr><td>Medium</td><td>500 MB and ≤ 800 MB</td><td>PC / Android</td></tr><tr><td>High</td><td>800 MB and ≤ 2 GB</td><td>PC</td></tr><tr><td>Very High</td><td>2 GB</td><td>PC (High-End only, with warning popup)</td></tr></tbody></table>

{% hint style="warning" %}
Worlds marked **Very High** will trigger a **warning popup** for users, indicating that the content is intended for high-end PCs only.
{% endhint %}

## Asset Optimization Tips

{% stepper %}
{% step %}
#### Model

Make sure models are game-ready by optimizing them for real-time rendering. Use a reasonable topology that minimizes polygon count. Small details such as text, bevels, and screws can be rendered using textures.
{% endstep %}

{% step %}
#### UV

Models need to contain two sets of UVs: UV1 for textures, and UV2 for lightmaps. Most 3D modeling software can make decent lightmaps. You can also use the PlayCanvas editor to generate lightmaps. See the [Lightmapping](https://developer.playcanvas.com/user-manual/graphics/lighting/lightmapping/) topic in the PlayCanvas User Manual for details.
{% endstep %}

{% step %}
#### Material

PlayCanvas uses the PBR with advanced shading to make sure rendering is as realistic as possible. Single objects can contain multiple materials, but performance should be prioritized (for example, by reducing draw calls). \*See the [Physical Material](https://developer.playcanvas.com/user-manual/graphics/physical-rendering/physical-materials/) topic in the PlayCanvas User Manual for details.
{% endstep %}

{% step %}
#### Texture

High texture counts can overload the GPU. Try to keep the texture resolution at a reasonable size. Image resolution can’t be higher than 2048x2048, for the best texture quality, use JPGs, PNGs, or TGAs. Try storing RMA textures in different channels to reduce texture count (for example, RGB: R – Ambient Occlusion; G – Roughness; B – Metallic).
{% endstep %}

{% step %}
#### Lighting

Real-time lighting drastically reduces performance (each new real-time light source results in two draw calls). Try using just one real-time light source to produce shadows for characters and dynamic objects. Use lightmaps for static objects. \*Additional HDRI cubemaps are required for reflective objects.
{% endstep %}

{% step %}
#### Collision

Make sure to use low-poly models as collision meshes for your scenes. Don’t use original high poly models as collision meshes. Also make sure the low-poly models are individual solid meshes. Don’t join and intersect multiple meshes.

<figure><img src=".gitbook/assets/image (355).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### Export

Name and group your models, then export them (preferably in GLB format). Make sure all textures and animations are packed, and then import them into PlayCanvas.
{% endstep %}
{% endstepper %}
