---
hidden: true
noIndex: true
---

# \[WIP] Starting with PlayCanvas Toolkit

## Introduction

PlayCanvas is an open-source game engine designed to advance the development of 3D web games, interactive content and rich multimedia. Unlike similar WebGL engines like Three.js or Babylon, PlayCanvas comes with sophisticated in-browser Editor (inspired by early Unity 2.x - 3.x), which provides its users with a rich set of tools — allowing to assemble scenes from imported assets, setup realtime and baked lighting, write custom scripts, create animation graphs, and much much more.

To make creator experience even smoother, VIVERSE has developed a special Extension for Chrome browsers which extends PlayCanvas Editor functionality with additional set of no-code tools and publishing options. It provides common building blocks like Triggers, Actions and Quests, enables Avatar system in your project, and allows one-click testing and publishing right from the Editor.

Below is a useful set of tutorials to help you get started with VIVERSE PlayCanvas Extension. We assume you're already familiar with PlayCanvas Editor itself and have PlayCanvas account. If not, please feel free to explore [PlayCanvas User Manual](https://developer.playcanvas.com/user-manual/) first!

## Install PlayCanvas Extension

{% columns %}
{% column width="66.66666666666666%" %}
{% stepper %}
{% step %}
#### Get the latest Extension from VIVERSE

* [Download](wip-starting-with-playcanvas-toolkit.md#download-the-latest-extension-version) the latest version of Playcanvas Extension
* Unzip downloaded file on your computer
{% endstep %}

{% step %}
#### Navigate to Chrome Extensions manager

* Click Extensions icon in Chrome toolbar and open the Extensions Popup
* Click Manage Extensions at the bottom and open the Extensions Manager in a new tab
{% endstep %}

{% step %}
#### Install the Extension

* Enable Developer Mode at the top right corner
* Click Load Unpacked and select the folder with unpacked Extension you downloaded previously
* Verify the Extension is now present in Extensions Manager tab
{% endstep %}
{% endstepper %}
{% endcolumn %}

{% column width="33.33333333333334%" %}
<div><figure><img src="../.gitbook/assets/1-1 (1).png" alt=""><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/2-2 (1).png" alt="" width="563"><figcaption></figcaption></figure></div>

<div><figure><img src="../.gitbook/assets/3-1 (1).png" alt="" width="375"><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/4 (2).png" alt=""><figcaption></figcaption></figure></div>

<div><figure><img src="../.gitbook/assets/5 (1) (1).png" alt="" width="188"><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/6 (2).png" alt="" width="375"><figcaption></figcaption></figure></div>

<figure><img src="../.gitbook/assets/7 (1).png" alt="" width="246"><figcaption></figcaption></figure>


{% endcolumn %}
{% endcolumns %}

## Create and publish your first project

{% columns %}
{% column width="66.66666666666666%" %}
{% stepper %}
{% step %}
#### Initialize your new project

* Create a new PlayCanvas project
* Login into VIVERSE via Extension
* Delete default **Camera** in Scene Hierarchy
{% endstep %}

{% step %}
#### Create a Floor entity

* Select the **Plane** entity and rename it **Floor**
* To increase the size of the **Floor,** change the **Scale** to **(20, 1, 20)**
* To allow the players to collide with the **Floor**, add a **Collision** component
* To increase the size of the **Collision** component so that it covers the entire **Floor**, change **Half Extents** to **(10, .1, 10)**
* To prevent the players from falling through the **Floor,** add a **Rigidbody** component
* Click the **Import Ammo** button
{% endstep %}

{% step %}
#### Create a Spawn Point

* Add a new **Entity** to the scene. Rename the entity to **SpawnPoint**
* Add `spawn-point` to the **Tags** field
* To have the spawn point above the ground and close to the wall, change the **Position** to **(0, 1, 8)**
* To change the direction of the **SpawnPoint** so that the player will face the ball when spawned in, change **Rotation** to **(0, 180, 0)**
{% endstep %}

{% step %}
#### Upload your project to the VIVERSE

* Click the **Publish / Download** to open up the **Builds** window
* In the **Builds** window, select the **Builds & Publish tab** on the left and click the **Publish to Viverse Create** button to upload the project. This may take a few minutes and a progress bar will show the upload status
{% endstep %}

{% step %}
#### Preview your project live

* After the project has been successfully published, click the **Preview** button to view the project
* Use the preview URL to verify the scene on VIVERSE Create. If necessary, return to the PlayCanvas Editor to make changes then re-publish the project
{% endstep %}

{% step %}
#### Create a new VIVERSE World

* After confirming everything is set in your preview URL, click the "**Create World**" button
* Give the World a name and click the "**Create**" button to publish it to the VIVERSE Create platform
* The resulting URL is the public link to the World. Your World can also be accessed by clicking the "**Go to World**" button
{% endstep %}

{% step %}
#### You're all set!

* The World should be viewable and the avatar can be used to interact with the world
{% endstep %}
{% endstepper %}
{% endcolumn %}

{% column width="33.33333333333334%" %}
<figure><img src="../.gitbook/assets/2-2.jpg" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/2-15.jpg" alt=""><figcaption></figcaption></figure>

<div><figure><img src="../.gitbook/assets/image (332).png" alt="" width="287"><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/image (333).png" alt="" width="287"><figcaption></figcaption></figure></div>

<figure><img src="../.gitbook/assets/image (334).png" alt="" width="375"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (335).png" alt="" width="375"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (339).png" alt="" width="368"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (341).png" alt="" width="375"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/2-16.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

{% embed url="https://www.youtube.com/watch?v=w3MFHo4v69E" %}

## "Factory Reset" the Extension

> Sometimes issues in the installation process can produce persistent bugs that are only fixed by fully resetting the Extension

1. Sign out of your account on viverse.com, and then sign back in. **MAKE SURE** you are using the same email account as the one associated with your PlayCanvas account
2. Go back to your PlayCanvas project and delete:
   * The `Extension` Entity from your scene hierarchy
   * The `@viverse` folder from your project assets
   * The `extension-image` folder from your project assets
   * The `extension-script` folder from your project assets
3. Open the [Developer Tools](https://elfsight.com/blog/how-to-work-with-developer-console/) in your Chrome brower, go to "Application", select "Storage" in the left hand toolbar and click the "Delete Site Data" button
4. Refresh your PlayCanvas project page with the VIVERSE extension enabled
5. Sign in to PlayCanvas when prompted. **MAKE SURE** you are using the same email account as the one associated with your VIVERSE account
6. Sign in to VIVERSE from within your PlayCanvas project using the VIVERSE Scene Settings

```
>>> Would benefit from illustrative video / images as well, the original page doesn't have any
```
