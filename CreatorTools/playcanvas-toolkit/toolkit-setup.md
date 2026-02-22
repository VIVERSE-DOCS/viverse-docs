---
description: >-
  Learn how to install the VIVERSE Chrome Extension for PlayCanvas Editor,
  create your first world, then publish it to VIVERSE.
---

# Toolkit Setup

***

### Introduction

PlayCanvas is an open-source game engine designed to advance the development of 3D web games, interactive content and rich multimedia. Unlike similar WebGL engines like Three.js or Babylon, PlayCanvas comes with sophisticated in-browser Editor (inspired by early Unity 2.x - 3.x), which provides its users with a rich set of tools — allowing to assemble scenes from imported assets, setup real-time and baked lighting, write custom scripts, create animation graphs, and much much more.

To make creator experience even smoother, VIVERSE has developed a special Extension for Chrome browsers which extends PlayCanvas Editor functionality with additional set of no-code tools and publishing options. It provides common building blocks like Triggers, Actions and Quests, enables Player locomotion and Avatar system in your project, and allows one-click testing and publishing right from the Editor.

Below is a useful set of tutorials to help you get started with VIVERSE PlayCanvas Extension. We assume you're already familiar with PlayCanvas Editor itself and have PlayCanvas account. If not, please feel free to explore [PlayCanvas User Manual](https://developer.playcanvas.com/user-manual/) first!

### Install PlayCanvas Extension

{% columns %}
{% column width="66.66666666666666%" %}
{% stepper %}
{% step %}
#### Get the latest Extension from VIVERSE

* [Download](playcanvas-toolkit-changelog.md) the latest version of Playcanvas Extension
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
<figure><img src="../.gitbook/assets/im12.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/im13.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/im14.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/im15.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/im16.png" alt=""><figcaption></figcaption></figure>


{% endcolumn %}
{% endcolumns %}

### Create and publish your first project

{% hint style="info" %}
Before proceeding any further, we assume you're familiar with the basics of PlayCanvas Editor. If you're looking for a comprehensive introduction to it — please refer to a dedicated [PlayCanvas Editor Manual](https://developer.playcanvas.com/user-manual/editor/) first!
{% endhint %}

{% columns %}
{% column width="66.66666666666666%" %}
{% stepper %}
{% step %}
#### Initialize your new project

* Create a new PlayCanvas project or open an already existing one
* Make sure VIVERSE Extension is properly initialized — you should see **ExtensionEntity** in your scene hierarchy and `@viverse` folder added to your project. Refresh the page if it's not the case
* Login into VIVERSE via dedicated button in the left toolbar. Once logged in, you should see **VIVERSE Scene Settings** button there
{% endstep %}

{% step %}
#### Setup 3D environment

* First, delete default **Camera** in Scene Hierarchy. The Extension will provide its own Camera system that will be added to your Scene at runtime
* Import **Ammo** physics library and add a Floor plane with **Collision** component and static **Rigidbody**. This is crucial because VIVERSE Player system relies on built-in PlayCanvas physics engine for moving around. Without a floor, your player will be simply falling through
* Add additional geometry and lights if want!
{% endstep %}

{% step %}
#### Create a Spawn Point

* In order for a Player to appear in your world, it should be spawned somewhere first. VIVERSE Extension provides a convenient way to control that!
* Simply create a new empty **Entity** in your scene and add `spawn-point` tag to it
* Make sure it's positioned at the floor level or above. Feel free to rotate it to change the initial orientation of your Player
{% endstep %}

{% step %}
#### Preview your project

* Once you have a walkable floor and a spawn point, it's time to test your project live!
* Go to the **Publish / Download** menu and open the **Builds** popup window. In that window, click **Publish to Viverse** button and wait until your project is successfully uploaded
* Once it's uploaded and ready, the **Preview** button should appear. Clicking it will open your project in a new tab, in preview mode. This preview URL is public and you can share it with your friends and colleagues to test the current version of your work
* Return back to PlayCanvas Editor and modify your project as you see fit. Keep publishing and previewing to see your most recent changes live in VIVERSE environment
{% endstep %}

{% step %}
#### Final: Create a VIVERSE World for your project

* After confirming everything is working correctly in preview mode, you can publish your project to production environment!
* In preview mode, click "**Create World**" button, give your World a name and click the "**Create**" button to publish it to the VIVERSE Create platform
* The resulting URL is the public link to your World. You can return back to your project any time and republish an updated version as you see fit
* Feel free to configure your [World Settings](../publishing-with-your-viverse-account.md#world-settings) as well
{% endstep %}
{% endstepper %}
{% endcolumn %}

{% column width="33.33333333333334%" %}
<figure><img src="../.gitbook/assets/im1.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/im2.png" alt=""><figcaption></figcaption></figure>

<div><figure><img src="../.gitbook/assets/im3a.png" alt=""><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/im3b.png" alt=""><figcaption></figcaption></figure></div>

<figure><img src="../.gitbook/assets/im4.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/im7.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/im5a.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/im5b.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/im8.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/im9a.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/im9b.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

{% embed url="https://www.youtube.com/watch?v=w3MFHo4v69E" %}

### "Factory Reset" PlayCanvas Extension

> Sometimes issues in the installation process can produce persistent bugs that are only fixed by fully resetting the Extension. If you're experiencing these bugs please follow the steps below

{% stepper %}
{% step %}
#### Sign in and out of VIVERSE

Sign out of your account on viverse.com and then sign back in. **MAKE SURE** you are using the same email account as the one associated with your PlayCanvas account
{% endstep %}

{% step %}
#### Delete VIVERSE entities from PlayCanvas project

Go back to your PlayCanvas project and delete:

* The `Extension` Entity from your scene hierarchy
* The `@viverse` folder from your project assets
* The `extension-image` folder from your project assets
* The `extension-script` folder from your project assets
{% endstep %}

{% step %}
#### Clear Application Storage

Open the [Developer Tools](https://elfsight.com/blog/how-to-work-with-developer-console/) in your Chrome brower, go to "Application", select "Storage" in the left hand toolbar and click the "Delete Site Data" button
{% endstep %}

{% step %}
#### Refresh the page

Refresh your PlayCanvas project page with the VIVERSE extension enabled
{% endstep %}

{% step %}
#### Sign in back to PlayCanvas

Sign in to PlayCanvas when prompted. **MAKE SURE** you are using the same email account as the one associated with your VIVERSE account
{% endstep %}

{% step %}
#### Sign in back to VIVERSE

Sign in to VIVERSE from within your PlayCanvas project using the VIVERSE Scene Settings
{% endstep %}
{% endstepper %}
