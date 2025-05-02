---
description: >-
  The page outlines the general optimization and design recommendations for all
  creators publishing on VIVERSE, regardless of which platform they are
  publishing from.
---

# Optimization

***

PlayCanvas is a visual development platform for creating interactive 3D web content. Both the tools and web apps you create with them are powered by HTML5. The platform is entirely cloud-based, thus no further installs are required - you are able to access your work on any device from a supported web browser.

{% hint style="info" %}
We strongly recommend taking a look at the PlayCanvas User Manual before getting started so you’ll have an idea about how to use PlayCanvas when you decide to jump in and start making games. [PlayCanvas Manual](https://developer.playcanvas.com/user-manual/)
{% endhint %}

VIVERSE Create uses PlayCanvas editor to create all the virtual worlds and environments you see in VIVERSE. This guide describes how to create a project and configure settings so you can start constructing your first scene.

<figure><img src=".gitbook/assets/image (346).png" alt="" width="375"><figcaption></figcaption></figure>

### **Creating A PlayCanvas Project**

{% stepper %}
{% step %}
#### Create New Project

Before you can start using PlayCanvas, you’ll need to create an account. After creating an account, go to your profile page and click the PROJECTS tab. Then click NEW in the upper right.

<figure><img src=".gitbook/assets/image (347).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### Naming Project

Enter a name for your project. The description is optional and can be filled in later. Click CREATE.

<figure><img src=".gitbook/assets/image (353).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### Click Edit Button

Now that you’ve started a project, you can dive right into the editor – just click the EDITOR button.

<figure><img src=".gitbook/assets/image (354).png" alt="" width="195"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}

## **3D Asset Guide**

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

### Recommended Parameter

The assets you create directly influence the look and the performance of your project. Recommended performance and asset resource parameters are listed below.

### Overall Performance

| FPS (frame rate) | 60 or higher    |
| ---------------- | --------------- |
| Draw Call        | Below 150       |
| VRAM             | Less than 400mb |
| System RAM       | less than 500mb |

### Asset resource

| Polycount          | Below 30,000 for each model                            |
| ------------------ | ------------------------------------------------------ |
| Texture format     | PNG/JPG/TGA                                            |
| Texture resolution | Up to 1024 x 1024                                      |
| Materials          | PBR shading; maximum material count: 50                |
| Rig bone count     | Below 60 (including leaf bones and non-weighted bones) |
| Bones per vertex   | 4 bones maximum                                        |
| Size of each file  | 60mb maximum                                           |

### Importing Assets

{% stepper %}
{% step %}
#### Create Folder

Go to ASSETS and create folders to import your assets into.

<figure><img src=".gitbook/assets/image (356).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### Import Model

Import your models. PlayCanvas will automatically extract textures, animation data, and other elements from GLB and FBX files and create folders for them.

<figure><img src=".gitbook/assets/image (359).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### Import Cubemap

Import your cubemaps. Go to Settings, search for RENDERING, then add the cubemap to the Skybox field.

<figure><img src=".gitbook/assets/image (360).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### Configure Collision

Configure collision settings. Add your low-poly collision models to the scene, click ADD COMPONENT, and then add two physics components: Collision and Rigid Body. For the Collision component, set Type to Mesh, add the intended low-poly model to Render Asset, and then turn off RENDER. Built-in non-mesh type collisions can also be used for models with simple shapes.

<figure><img src=".gitbook/assets/image (361).png" alt="" width="375"><figcaption></figcaption></figure>

Built-in non-mesh type collisions can also be used for models with simple shapes.

<figure><img src=".gitbook/assets/image (362).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### Configure Model Settings

If the model is a static object in the scene, select Static for the Render component. If the models share the same material and mesh, use Batch Group to reduce the number of draw calls. \*See the \[Batching]\(https://developer.playcanvas.com/user-manual/graphics/advanced-rendering/batching/ topic in the PlayCanvas User Manual for details.

<figure><img src=".gitbook/assets/image (363).png" alt="" width="375"><figcaption></figcaption></figure>

See the [Batching](https://developer.playcanvas.com/en/user-manual/optimization/batching/) topic in the PlayCanvas User Manual for details.
{% endstep %}

{% step %}
#### Configure Material Settings

Set Shading to Physical and place each texture in the correct channel.

<figure><img src=".gitbook/assets/image (364).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### Configure Texture Settings

Click the image to open the properties panel, then select BASIS in the compression properties window. Click COMPRESS BASIS to complete texture compression. The maximum resolution for textures is 1024 X 1024.

<figure><img src=".gitbook/assets/image (366).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### Configure Lighting Settings

Add a directional light for characters and dynamic objects, and then in the light properties window, select Static and Cast Shadows. If you bake lightmaps in PlayCanvas, remember to disable all baked light, except directional light. If you don’t disable baked light, the scene may crash.

<figure><img src=".gitbook/assets/image (367).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### Inspect Performance

Turn on the performance monitoring tools (Profiler, Debug, Mini stats, etc.) under the Launch button to check things like draw-calls, VRAM, and FPS. This way, can check whether the scene is overloaded or what’s causing a scene to crash. Make sure all performance data is aligned with the suggested stats.

<figure><img src=".gitbook/assets/image (369).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### Private Project

Go back to project page and set the project to PRIVATE to prevent any data leakage. Under the TEAM heading, you can authorize other accounts to view or edit projects for better collaboration

<figure><img src=".gitbook/assets/image (368).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}
