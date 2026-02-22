---
description: >-
  Nirvana Grove is a tranquil bamboo forest designed for ultimate wellness
  developed with Unity 6 for the Viverse Platform by Thorium Labs.
hidden: true
noIndex: true
---

# NIRVANA GROVE by Thorium Labs

***

## Project Overview

Nirvana Grove is a bamboo forest developed with Unity 6. The Terrain tool was used to create the\
landscape, we carefully chose the vegetation, composed of bamboo, grass, ferns and flowers, the\
correct textures and a set of Japanese decorative elements to compose the environment.

### Project Resources

Nirvana Grove can be experienced here: [https://create.viverse.com/FPJWhRP](https://create.viverse.com/FPJWhRP)

An overview of this project can be found here: {Link TBD}

### Core Concepts and Techniques

* Mountainous terrain, intersected by paths and a large lake
* Lush and varied vegetation
* Soundtrack composed of three special nature effects and a calming song
* Japanese decorative elements with special meanings.

***

## Project Creation

### Terrain Generation

We have used Unity’s Terrain tool to build the landscape, a mountainous terrain, intersected by paths\
with a huge lake as a backdrop.

#### 1 - Topology

The terrain is a 1000 by 1000 meters square, and up to 600 meters high. Here is the overview of the\
terrain and the main settings:

<figure><img src="../../.gitbook/assets/Screenshot 2025-07-18 at 10.38.12 AM.png" alt="" width="563"><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Screenshot 2025-07-18 at 10.39.10 AM.png" alt=""><figcaption></figcaption></figure>

#### 2 - Textures

We have used only four textures to depict each detail of the terrain as follows:

| Texture                                         | Name   | Description                                                                                                                                      |
| ----------------------------------------------- | ------ | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| ![](<../../.gitbook/assets/Texture Soil.jpg>)   | Soil   | <p>This is where you’ll find all the<br>plants of the terrain.</p>                                                                               |
| ![](<../../.gitbook/assets/Texture Dirt.jpg>)   | Dirt   | <p>It’s used to separate the<br>planted area from the paving.</p>                                                                                |
| ![](<../../.gitbook/assets/Texture Paving.jpg>) | Paving | <p>The pathway area used by the<br>avatars to walk through.</p>                                                                                  |
| ![](<../../.gitbook/assets/Texture Sand.jpg>)   | Sand   | <p>Used for the bottom of the<br>water bodies. This area can’t<br>be accessed by the avatars. It’s<br>only seen through the water<br>shader.</p> |

#### 3 - Vegetation

The vegetation plays the most prominent role in the space, but even so, we were very concerned\
about performance and minimized the use of prefabs. The plant textures have a crucial role, as\
avatars can walk among them too.

| Plant                                        | Name        | Type    | Description                                                                                                                            |
| -------------------------------------------- | ----------- | ------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| ![](<../../.gitbook/assets/Bamboo 1.png>)    | Bamboo 1    | Prefab  | <p>Mainly used in the<br>boundary area<br>between the ground<br>and the pavement to<br>create the “tunnel”<br>effect.</p>              |
| ![](<../../.gitbook/assets/Bamboo 2.png>)    | Bamboo 2    | Prefab  | <p>It’s the core element<br>of the bamboo forest,<br>covering all the main<br>bamboo groves.</p>                                       |
| ![](../../.gitbook/assets/Grass.png)         | Grass       | Texture | <p>The main element of<br>the soil, filling the<br>gaps between the<br>bamboo tree, ferns<br>and flowers.</p>                          |
| ![](<../../.gitbook/assets/Fern 1.png>)      | Fern 1      | Texture | <p>Ferns are randomly<br>distributed through<br>the soil.</p>                                                                          |
| ![](<../../.gitbook/assets/Fern 2.png>)      | Fern 2      | Texture | <p>Ferns are randomly<br>distributed through<br>the soil.</p>                                                                          |
| ![](<../../.gitbook/assets/Fern 3.png>)      | Fern 3      | Texture | <p>Ferns are randomly<br>distributed through<br>the soil.</p>                                                                          |
| ![](../../.gitbook/assets/Flower.png)        | Flower      | Texture | <p>Used to create areas<br>of emphasis,<br>interrupting the<br>sequence of green<br>areas with its white<br>tones.</p>                 |
| ![](<../../.gitbook/assets/Bamboo Leaf.png>) | Bamboo Leaf | Texture | <p>Used in the particles<br>systems to simulate<br>the bamboo falling<br>leaves, working in<br>tune with the wind<br>sound effect.</p> |

### Sound Elements

The soundtrack is composed of three special nature effects and a calming song to compose the\
landscape and immerse the avatar.

| Sound                          | Type         | Description                                                               |
| ------------------------------ | ------------ | ------------------------------------------------------------------------- |
| Cosmic Breaths of the Universe | Music        | <p>A low frequency music that<br>inspires relaxation and<br>calmness.</p> |
| Birds                          | Sound Effect | Several chirping birds                                                    |
| Wind                           | Sound Effect | <p>Wind blowing over the forest<br>and gently swaying the leaves</p>      |
| Water                          | Sound Effect | A gentle water flow.                                                      |

### Decoration

The decoration is another important part of the space as it makes the user know that he’s in a\
japanese garden.

| Item                                              | Name             | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------- | ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ![](<../../.gitbook/assets/Torii Gate.png>)       | Torii Gate       | <p>A Torii gate is a traditional Japanese gate most<br>commonly found at the entrance of or within a<br>Shinto shrine, marking the transition from the<br>mundane to the sacred. It symbolizes the<br>boundary between the human world and the<br>world of the kami (spirits or deities). Passing<br>through a torii signifies entering a sacred<br>space.</p>                                                                                                    |
| ![](<../../.gitbook/assets/Dog Statues.png>)      | Dog Statues      | <p>In Japanese gardens, dog statues, often called<br>Komainu, symbolize protection and are meant<br>to ward off evil spirits. They are typically placed<br>in pairs at the entrance of shrines, temples,<br>and sometimes even homes, acting as<br>guardians. One statue has its mouth open (Agyo), representing the beginning, while the<br>other has its mouth closed (Un-gyo),<br>representing the end, together symbolizing the<br>totality of existence.</p> |
| ![](../../.gitbook/assets/Shrine.png)             | Shrine           | <p>In a Japanese garden, a small shrine (or<br>hokora) is a miniature representation of a<br>Shinto shrine, serving as a sacred space for<br>venerating kami, spirits or deities associated<br>with nature, ancestors, or historical<br>figures. These shrines are often integrated into<br>the garden's design to connect the physical<br>space with the spiritual realm and foster a<br>sense of harmony and respect for nature.</p>                            |
| ![](../../.gitbook/assets/Lanterns.png)           | Lanterns         | <p>Japanese lanterns, or tōrō, are more than just<br>decorative elements; they symbolize light,<br>hope, and are deeply connected to Japanese<br>culture and spirituality. They often appear in<br>gardens, temples, and during festivals, guiding<br>spirits and creating a serene atmosphere.</p>                                                                                                                                                               |
| ![](<../../.gitbook/assets/Stairs and Walls.png>) | Stairs and Walls | <p>These elements are merely decorative and help<br>to drive the avatar through the scene. They<br>don’t have any special meaning besides the<br>structural one.</p>                                                                                                                                                                                                                                                                                              |

### Lighting, Skybox and Cloud System

These are the final elements that wrap up the scene. They create the dramatic backdrop to create an\
even more immersive experience.

| Item                                                               | Type              | Description                                                                                                                                                                              |
| ------------------------------------------------------------------ | ----------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ![](<../../.gitbook/assets/Directional Light Settings I.png>)      | Directional Light | <p>This is the main light source of<br>the scene and the one that<br>creates the whole sunset<br>atmosphere. It’s aligned with<br>the sun at the Skybox.</p>                             |
| ![](<../../.gitbook/assets/Directional Light Settings II (1).png>) | Directional Light | <p>This is the second light source<br>of the scene and it helps to<br>create more lighting and<br>shadows against the main<br>light source.</p>                                          |
| ![](../../.gitbook/assets/Skybox.png)                              | Skybox            | <p>A sunset Skybox with a distant<br>moon and stars.</p>                                                                                                                                 |
| ![](<../../.gitbook/assets/Cloud System.png>)                      | Cloud System      | <p>One of my favorite items to<br>compose a scene. It has two<br>levels of clouds, low and high,<br>with separate settings like<br>cloud format, density,<br>amount, speed and color</p> |

***

## Publish to VIVERSE

Here are the next steps in order to have your space published to VIVERSE.

{% stepper %}
{% step %}
### Login to VIVERSE

Open a terminal session and type `viverse-cli auth login` and provide your\
credentials.<br>

<div align="left"><figure><img src="../../.gitbook/assets/Login to Viverse.png" alt=""><figcaption></figcaption></figure></div>
{% endstep %}

{% step %}
### Change directory

Change to the directory where your build is located `cd D:\Unity\4 - HTC Viverse\Viverse`\
`Test\WebGL Builds`
{% endstep %}

{% step %}
### Create App

Create the VIVERSE app by typing `viverse-cli app create`. You’ll get the App ID of your space to be used when publishing it.
{% endstep %}

{% step %}
### Publish

Publish to VIVERSE by typing viverse-cli app publish `D:\Unity\4 - HTC Viverse\Viverse`\
`Test\WebGL Builds\Build --app-id c47rbqg6v8`, with the full path or `viverse-cli app publish --app-id c47rbqg6v8` if you are already located in the right directory

<div align="left"><figure><img src="../../.gitbook/assets/Publish to Viverse.png" alt=""><figcaption></figcaption></figure></div>
{% endstep %}

{% step %}
### Preview Content

Navigate to the preview url created for the world. You can also access the world and its settings in studio.viverse.com/content

<figure><img src="../../.gitbook/assets/Navigate to the preview.png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Submit for Approval

By default, worlds uploaded will only be accessible via preview urls. For placement and curation on our webpages, meaning your experience will be easier to share, please submit for review.

<figure><img src="../../.gitbook/assets/Submit for review.png" alt=""><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}
