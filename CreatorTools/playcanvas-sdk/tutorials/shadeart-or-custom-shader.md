---
description: >-
  This page outlines the usage of the custom shader components contributed by
  ShuShu VR and Niko Lang as part of their commission for VIVERSE.
---

# SHADEART | Custom Shader

***

## Introduction

The following specifications and guidelines will allow you to use our custom shaders in your own project. The project contains custom shaders, custom textures, materials and audio files.

You may use the Shaders, Models, Materials, cube Maps, Seamless Textures and Equirectangular Maps, yet, due to copyright, you are not permitted to use any other assets (such as 3rd party scripts: Audio files, Fonts, Noise Maps or Normal Maps).

**Project Title:** ShadeArt > Custom shaders showcase \[VIVERSE VR Development Workshop -Experimental Shaders Development Space].\
**VIVERSE world link:** [https://create.viverse.com/mkEVXZz](https://create.viverse.com/mkEVXZz)\
**Project link:** [https://playcanvas.com/editor/scene/2163408?v2](https://playcanvas.com/editor/scene/2163408?v2)\
**PlayCanvas Project File:** [https://github.com/VIVERSE-DOCS/viverse-docs/raw/refs/heads/main/samples/ShadeArt\_03302025\_ShuShuVR\&NikoLang.zip](https://github.com/VIVERSE-DOCS/viverse-docs/raw/refs/heads/main/samples/ShadeArt_03302025_ShuShuVR\&NikoLang.zip)\
**Engine 2.5.2 (Engine v2)**\
**Authors:** Niko Lang & ShuShu VR\
**Questions?** Contact through VIVERSE Creators Discord channel

### Custom shaders included in this project

1. [EquiProjection](shadeart-or-custom-shader.md#equiprojection) (Skybox shader for Equirectangular Map)
2. [EquiProjectionDistort](shadeart-or-custom-shader.md#equiprojectiondistort) (Skybox shader for Equirectangular Map, with Distortion Map and Additive Equirectangular Map)
3. [EquiProjectionUVDistort](shadeart-or-custom-shader.md#equiprojectionuvdistort) (Projection Shader for seamless textures).
4. [Water Shader](shadeart-or-custom-shader.md#water-shader) (Amplify Water)

{% embed url="https://1140075852-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F4pMiThqqrBzfvP8uy5am%2Fuploads%2Fn9wTC8qa8qOKVTTirmlV%2FUntitled.mp4?alt=media&token=9ff76f93-c871-4f42-8633-b5ee9398d172" %}

### Important Notes

1. The standard Play Canvas Editor does not allow a preview of the custom shaders. You will have to publish the world and check the result in game.
2. All materials applied in the custom shaders function only as the material "place holders" and are not displayed in game. You may use any standard material in this purpose.
3. The textures and maps presented in the ShadeArt gallery in VIVERSE are used as examples only. You may apply any other image instead of the existing images.

### Supporting Multiple Camera Perspectives

A major part of the custom shaders development was to adjust the shaders to work properly with the different camera perspectives used in VIVERSE.

Here is the Camera Function settings in each of the shader scripts:

```javascript
function getActiveCameraPosition(app) {
    var cameraNames = ['CAMERA_AUTO_ROTATE', 'CAMERA_1ST', 'CAMERA_OVERLAP', 'CAMERA_VR'];
    var camera = null;

    for (var i = 0; i < cameraNames.length; i++) {
        camera = app.root.findByName(cameraNames[i]);
        if (camera && camera.enabled) {
            var pos = camera.getPosition();
            return [pos.x, pos.y, pos.z]; 
        }
    }

    return [0, 0, 0]; 
}
```

You may reuse this for your own custom shaders development.

\---ShuShu & Niko

***

## EquiProjection (Skybox Shader)

### Overview

This is a skybox shader for Equirectangular Map (2x1 texture) assigned on an inverted sphere. The shader ensures that the skybox is correctly projected onto the scene in such a way that it always appears at an infinite distance, which means it gives the illusion of an infinite environment around the player.

No matter how the camera (User) moves, the skybox doesn’t change in size nor resolution, and it stays fixed in the background. The shader do not relate to any UV maps and can by applied on any type of mesh with inverted normals/faces.

<figure><img src="../../.gitbook/assets/Screenshot 2025-03-31 at 9.56.09 AM.png" alt=""><figcaption></figcaption></figure>

### Setting the Shader

{% stepper %}
{% step %}
#### The skybox mesh

Import a sphere with inverted normals/faces into your Play Canvas project. We recommend using the sphere model-template from our shared project (see: ScenesAssets > Models > Common > ProjSphere1x1\_64x32\_Inv).

The sphere size is 1 m. x 1 m. in diameter. Scale it to 100 m. or any other size that will fit to your environment. Scaling the mesh up or down will not affect the skybox image resolution.

Place the sphere in the Hierarchy.
{% endstep %}

{% step %}
#### Assigning the shader to the mesh

Assign the script "equiprojection.js" (see: ScenesAssets > Shaders > 01\_Equirpojection > Scripts) to the sphere Template Instance (parent).
{% endstep %}

{% step %}
#### Assigning the shader components to the shader script

Assign the Vertex and Fragment shaders (euiprojection\_fs / equiprojection\_vs) to the Script (see: ScenesAssets > Shaders > 01\_Equiprojection > Shaders).
{% endstep %}

{% step %}
#### Assigning an Equirectangular Map to the shader script

The Equirectangular Map is an image with a ratio of 2x1. A 4K image size is 4096x2048 pixels, while 8K is 8192x4096 pixels. In our sample project we use 8K images.

Upload an e. map into your project.

Assign the e. map to the shader script.

Notes :\
Make sure to uncheck the Mipmaps in the texture settings.\
You do not need to generate a Play Canvas cube map, simply link your Equirectangular Map to the shader.
{% endstep %}
{% endstepper %}

### Factors

{% tabs %}
{% tab title="Rotation" %}
If you wish to rotate the skybox image (the equirectangular map) remember, rotating the mesh itself will not affect the image rotation.

In order to rotate the image, use the Rotation parameters in the script settings.

Notes

In the immersive world, an equirectangular map will usually be displayed differently than it would display in Photoshop or a any image preview app.

It will be rotated 90 d. to the right (for example, if you are using 1 8192x4096 image, where the center of your image is at 4096, in the immersive world the center will be at the 6144 pixels position, which is 90 d. to the right).

The skybox Rotation parameters : 0.25 rotates the image by 2048 pixels (90) d., 0.5 by 4096 pixels (180 d.), 0.75 by 3072 pixels (270 d.) and 1 by 360 d.
{% endtab %}
{% endtabs %}

***

## EquiProjectionDistort (Skybox Shader)

### Overview

This is an upgraded version of the Equiprojection Shader mentioned above. The shader has an additive layer called "Equirectangular Map Stars" and a Distortion Map (Noise Map).

You may review the shader in our ShadeArt gallery in VIVERSE (see: skybox Super Star, Space Nebula and Galactic Abyss).

<figure><img src="../../.gitbook/assets/Screenshot 2025-03-31 at 9.56.38 AM.png" alt="" width="411"><figcaption></figcaption></figure>

### Setting the Shader

{% stepper %}
{% step %}
#### The skybox mesh

Import a sphere with inverted normals/faces into your Play Canvas project.

We recommend using the sphere model-template from our shared project (see: ScenesAssets > Models ? Common > ProjSphere1x1\_64x32\_Inv).

The sphere size is 1 m. x 1 m. in diameter. Scale it to 100 m. or any other size that will fit to your environment. Scaling the mesh up or down will not affect the skybox image resolution.

Place the sphere in the Hierarchy.
{% endstep %}

{% step %}
#### Assigning the shader to the mesh

Assign the script "equiprojection-distort.js" (see: ScenesAssets > Shaders > 02\_EquirojectionDistort > Scripts) to the sphere Template Instance (parent).
{% endstep %}

{% step %}
#### Assigning the shader components to the shader script

Assign the Vertex and Fragment shaders (euiprojection-distort\_fs / equiprojection-distort\_vs) to the Script (see: ScenesAssets > Shaders > 02\_EquirojectionDistort > Shaders).
{% endstep %}

{% step %}
#### Assigning an Equirectangular Map to the shader script

The Equirectangular Map is an image with a ratio of 2x1. A 4K image size is 4096x2048 pixels, while 8K is 8192x4096 pixels. In our sample project we use 8K images.

Upload an e. map into your project.

Assign the e. map to the shader script in section : Equirectangular Map.

Notes :\
Make sure to uncheck the Mipmaps in the texture settings.\
You do not need to generate a Play Canvas cube map, simply link your Equirectangular Map to the shader.
{% endstep %}

{% step %}
#### Assigning the Additive Equirectangular Map

This is where you assign an additive equirectangular map (see additive map example in: ScenesAssets > Textures\_Equirectangular > SkyBox\_Stars)

It can be an image with stars on a black background or any other graphics, such as logo - make sure the black is set to R=0 G=0 B=0).

Assign the Map in the in section : Equirectangular Map Stars.
{% endstep %}

{% step %}
#### Assigning the Distortion Map (Noise Map)

Upload a Noise Map to your project and assign it to the shader script. This map will affect the distortion effect. You may use any Noise Map and check the result in game.
{% endstep %}
{% endstepper %}

### Factors

{% tabs %}
{% tab title="Noise Tiling Factor" %}
Chose your preferred distortion tiling.

The noise map tiling will not affect the equirectangular tiling.
{% endtab %}

{% tab title="Noise Movement Speed" %}
This factor will affect the speed of the distortion created by the Noise Map.
{% endtab %}

{% tab title="Rotation" %}
If you wish to rotate the skybox image (the equirectangular map) remember, rotating the mesh itself will not affect the image rotation.

In order to rotate the image, use the Rotation parameters in the script settings.

Notes:

In the immersive world, an equirectangular map will usually be displayed differently than it would display in Photoshop or any image preview app.

It will be rotated 90 d. to the right (for example, if you are using 1 8192x4096 image, where the center of your image is at 4096, in the immersive world the center will be at the 6144 pixels position, which is 90 d. to the right).

The skybox Rotation parameters : 0.25 rotates the image by 2048 pixels (90) d., 0.5 by 4096 pixels (180 d.), 0.75 by 3072 pixels (270 d.) and 1 by 360 d.
{% endtab %}

{% tab title="Distortion Strength" %}
This factor will affect the intensity of the distortion effect.
{% endtab %}
{% endtabs %}

***

## EquiProjectionUVDistort

### Overview

This is a projection shader for seamless textures with a noise movement factor and a background equirectangular map. You may review the shader in our ShadeArt gallery in VIVERSE (see: Equiprojection UV distort samples in the tunnels).

The shader may be applied on any mesh with inverted normals/faces: on a floor, a wall, a sphere or any other model. This shader reacts to the mesh UV. It includes a Distortion Map that allows to generate a movement of the image.

<figure><img src="../../.gitbook/assets/Screenshot 2025-03-31 at 9.56.51 AM.png" alt="" width="409"><figcaption></figcaption></figure>

### Setting the Shader

{% stepper %}
{% step %}
#### The model (mesh)

Import any model with inverted normals/faces into your Play Canvas project. Make sure the mesh has correct UV mapping.

Feel free to experiment the results with various UV Mapping (an interesting effect can be achieved when the mesh has Follow active Quads mapping).

Place the sphere in the Hierarchy.
{% endstep %}

{% step %}
#### Assigning the shader to the mesh

Assign the script "equiprojection-uvdistort.js" (see: ScenesAssets > Shaders > 03\_EquiprojectionUVDistort > Scripts) to the model Template Instance (parent).
{% endstep %}

{% step %}
#### Assigning the shader components to the shader script

Assign the Vertex and Fragment shaders (euiprojection-uvdistort\_fs / equiprojection-uvdistort\_vs) to the Script (see: ScenesAssets > Shaders > 03\_EquiprojectionUVDistort > Shaders).
{% endstep %}

{% step %}
#### Assigning the background equirectangular map

This is where you assign an equirectangular map that will serve as the background image (see additive map example in: ScenesAssets > Textures\_Equirectangular > SkyBox\_Stars).

Upload an equirectangular image to your project and assign it to the shader script
{% endstep %}

{% step %}
#### Assigning the additive map (seamless texture)

Upload a seamless texture with a ratio of 1x1 (1 K is 1024x1024 pixels, 2K is 2048x2048 pixels, 4K is 40996x4096 pixels).

The images presented in our shared project and in the ShaderArt gallery in VIVERSE are mostly AI generated with some Photoshop post production. You may place your own image as long as it is a seamless texture.

Apply the texture to the shader script in the Additive Map section.

Notes :\
Make sure to uncheck the Mipmaps in the texture settings.
{% endstep %}

{% step %}
#### Assigning the distortion map (noise map)

This map will affect the distortion effect.

In the examples seen in our shared project and in the ShadeArt gallery in VIVERSE, the Distortion Map is the same texture used as an Additive Map with our without a photoshop effect (blur and B\&W filters). See distortion maps examples in: ScenesAssets > TexturesTilable.

Apply the Distortion Map to the shader script in the section: Distortion Map.

Notes :\
Make sure to uncheck the Mipmaps in the texture settings.
{% endstep %}
{% endstepper %}

### Factors

{% tabs %}
{% tab title="Noise Tiling Factor" %}
Chose your preferred distortion tiling.

The noise map tiling will not affect the equirectangular tiling, yet it will react to the mesh UV mapping.
{% endtab %}

{% tab title="Noise Movement Speed" %}
This factor will affect the speed of the distortion created by the Distortion Map. It may be set to positive or negative values (for example: 0.05 or -0.05).
{% endtab %}
{% endtabs %}

***

## Amplify Water Shader

### Overview

This is a custom made water shader that reacts to the mesh UV and may be applied on any mesh. You may review the shader in our ShadeArt gallery in VIVERSE (see: CyberCity). This shader reacts to the mesh UV. It includes a normal Map that allows to generate the movement of the water surface (waves).

<figure><img src="../../.gitbook/assets/Screenshot 2025-03-31 at 9.57.01 AM.png" alt="" width="411"><figcaption></figcaption></figure>

### Setting the Shader

{% stepper %}
{% step %}
#### The model (mesh)

Import any model with into your Play Canvas project. Make sure the mesh has correct UV mapping since the normal map applied to the shader will reacts to the UV mapping.

For a simple water surface use a circle shaped plane with Project From View (fit to bounds) UV mapping.

You may use the circle-plane model-template from our shared project (see: ScenesAssets > Models ? Common > CirclePlane1x1m128).The sphere size is 1 m. x 1 m. in diameter. Scale it to any size that will fit to your environment.

Scaling the mesh up or down will affect the normal map tiling.

Place the model Template in the Hierarchy.
{% endstep %}

{% step %}
#### Assigning the shader to the mesh

Assign the script "waterShadert.js" (see: ScenesAssets > Shaders > 04\_WaterShadert > Scripts) to the model Template Instance (parent).
{% endstep %}

{% step %}
#### Assigning the shader components to the shader script

Assign the Vertex and Fragment shaders (water\_fs / water\_vs) to the Script (see: ScenesAssets > Shaders > > 04\_WaterShader > Shaders).
{% endstep %}

{% step %}
#### Assigning the cube map

Just like in reality, a water without a reflection will not be visible. To achieve the water effect, you need to apply a Cube Map to the section : Cube Map.

Upload a Cube Map into your project and assign it to the shader script.

Creating a Cube Map from an Equirectangular Map:\
If you wish to create a Cube Map from the same Equirectangular Map you use in the skybox shader, you will need to generate a Cube Map from the Equirectangular Map. To do so, us the Play Canvas texture tool:

https://playcanvas.com/texture-tool

see explanation in : https://developer.playcanvas.com/user-manual/assets/types/cubemap/
{% endstep %}

{% step %}
#### Assigning the normal map

The Normal Map will generate the distortion effect, such as waves movement. See normal map example in: ScenesAssets > Shaders > > 04\_WaterShader > Textures.

Apply the Normal Map to the shader script in the section: Normal Map.
{% endstep %}
{% endstepper %}

### Factors

{% tabs %}
{% tab title="Normal Map Tiling Factor" %}
Chose your preferred distortion tiling.
{% endtab %}

{% tab title="Normal Map Strength" %}
This factor will affect the intensity of the distortion effect.
{% endtab %}

{% tab title="Base Colour" %}
The Base Colour serves as a colour filter. Complete Black will make the water look highly reflective.
{% endtab %}

{% tab title="Speed" %}
This factor will affect the speed of the distortion effect created by the Normal Map.
{% endtab %}

{% tab title="Reflectivity" %}
This factor will influence the intensity of the reflection onto the water surface.
{% endtab %}

{% tab title="Reflective Brightness" %}
Set this factor to achieve more or less visible reflection.
{% endtab %}

{% tab title="Fresnel Strength" %}
Set this factor to achieve more or less visible water surface.
{% endtab %}
{% endtabs %}
