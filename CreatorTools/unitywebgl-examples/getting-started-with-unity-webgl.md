---
description: >-
  This document provides a guide for creating a sample app in Unity, building
  the app for WebGL, and deploying the app to VIVERSE.
---

# Getting Started with Unity WebGL

***

## Introduction

Anyone can publish their WebGL-compatible Unity project to VIVERSE in a few simple steps. In this guide, we'll walk through the process of creating an new Unity project, making sure it is compatible with WebGL, and publishing to VIVERSE using the [VIVERSE CLI](https://www.npmjs.com/package/@viverse/cli).

{% include "../.gitbook/includes/notice-upload.md" %}

While VIVERSE is a great place for multiplayer games with networked avatars — and we have a number of services that can help you implement these features — it is not required to implement networked avatars to publish to VIVERSE.

## Prerequisites

* Unity Hub and Unity with the WebGL platform for that version of unity installed on your device, in the hub you should see the WebGL platform module next to the version of unity you're using.\
  <img src="../.gitbook/assets/image (730).png" alt="" data-size="original">

{% hint style="warning" %}
In this tutorial, we will be using Unity v6.1, however any WebGL-compatible version of Unity should be supported.
{% endhint %}

## A. Configure Your Unity Project

{% stepper %}
{% step %}
### Create a Unity Project

<figure><img src="../.gitbook/assets/Screenshot 2025-06-09 at 7.19.18 PM.png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Install the Unity Plugin

Go to the Package manager

Press the "Plus icon" and select "Add from git URL", and add the url "git@github.com:ViveDeveloperRelations/ViverseUnitySDK.git"

&#x20;![](<../.gitbook/assets/image (731).png>)
{% endstep %}

{% step %}
### Switch your build to the WebGL platform in the Build Profiles

Select Switch platform if you're not currently on the webgl settings page

<figure><img src="../.gitbook/assets/image (734).png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Open the menu Tools/WebGL Build Settings

<figure><img src="../.gitbook/assets/image (732).png" alt=""><figcaption></figcaption></figure>

It should look like the above, select "Apply WebGL Settings" to apply the appropriate settings

Selecting auto-zip builds after completion will allow you to quickly upload builds to the studio.viverse.com site

The selection for decompression fallback and other relevant settings will be there&#x20;
{% endstep %}

{% step %}
### If planning to use avatars, import the VRM packages by pressing the "Install VRM Packages" button

<figure><img src="../.gitbook/assets/image (735).png" alt=""><figcaption></figcaption></figure>


{% endstep %}

{% step %}
### Add Missing shaders after importing VRM related packages to ensure proper rendering of avatars that are dynamically loaded

<div><figure><img src="../.gitbook/assets/image (736).png" alt=""><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/image (738).png" alt=""><figcaption></figcaption></figure></div>
{% endstep %}

{% step %}
### Import sample scenes

<figure><img src="../.gitbook/assets/image (740).png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Optionally add the configurable driver scene to the build settings

This will help you familiar with the SDK lifecycle, how to configure and use the sdk visually![](<../.gitbook/assets/image (741).png>)
{% endstep %}
{% endstepper %}

## B. Build and Publish to VIVERSE

{% stepper %}
{% step %}
### Build your project

Go to build profiles and select "Build", as build and run will not allow you to use Viverse functionality - you must publish the app to be able to use it at the moment ![](<../.gitbook/assets/image (742).png>)
{% endstep %}

{% step %}
### Install the VIVERSE CLI

In a terminal session, run `npm install -g @viverse/cli` to install the CLI globally. Make sure you are using at least node v22.

<figure><img src="../.gitbook/assets/Screenshot 2025-06-09 at 7.34.52 PM.png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Login to VIVERSE

In the terminal session, run `viverse-cli auth login` and enter your VIVERSE account email and password.

<figure><img src="../.gitbook/assets/Screenshot 2025-06-09 at 7.35.19 PM.png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Create VIVERSE App

In the terminal session, run `viverse-cli app create` . Once complete, copy the app ID to be used when publishing.

<figure><img src="../.gitbook/assets/Image 6-12-25 at 1.06 PM.jpg" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Publish to VIVERSE

In the terminal session, run `viverse-cli app publish {path/to/unity/webgl/build} --app-id {your app id from step 4}` referencing folder containing the index.html of your Unity build.

<figure><img src="../.gitbook/assets/Screenshot 2025-06-09 at 7.35.45 PM.png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Test & Configure World Settings

Navigate to the preview url created for the world. You can also access the world and its settings in [studio.viverse.com/content](https://studio.viverse.com/content).&#x20;

<figure><img src="../.gitbook/assets/Screenshot 2025-06-09 at 7.45.53 PM.png" alt="" width="375"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2025-06-09 at 8.04.00 PM.png" alt="" width="316"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Submit for Curation and Discovery

By default, worlds uploaded will only be accessible via preview urls. For placement and curation on our webpages, meaning your experience will be easier to share, please [submit for review](../publishing-with-your-viverse-account.md#upload).
{% endstep %}

{% step %}
### Iterate, Learn, Explore!

In addition to the sample scenes, the rough flow of api usage can be reviewed at [https://github.com/ViveDeveloperRelations/ViverseUnitySDK/blob/master/Unity\_Viverse\_SDK\_Developer\_Guide.md](https://github.com/ViveDeveloperRelations/ViverseUnitySDK/blob/master/Unity_Viverse_SDK_Developer_Guide.md)\
\
And overview of the current version of the sdk with additional hints and tips at\
[https://github.com/ViveDeveloperRelations/ViverseUnitySDK?tab=readme-ov-file#viverse-unity-sdk-for-webgl](https://github.com/ViveDeveloperRelations/ViverseUnitySDK?tab=readme-ov-file#viverse-unity-sdk-for-webgl)
{% endstep %}
{% endstepper %}
