---
description: >-
  This document provides a guide for exporting a Godot project for HTML 5 and
  publishing to VIVERSE.
---

# Godot HTML5

***

### Introduction

In this getting-started guide, we will cover the basics of setting up a [Godot HTML5](https://docs.godotengine.org/en/latest/tutorials/export/exporting_for_web.html) project and publishing to VIVERSE using [the VIVERSE CLI](https://www.npmjs.com/package/@viverse/cli).

{% include ".gitbook/includes/notice-upload.md" %}

### Project Settings for Godot

Web support for Godot currently requires the engine version to be Godot 4.1 or higher for successful exports. **Currently using Godot without C# is essential**. However, web export support for C# is expected soon from a recent [announcement](https://godotengine.org/article/live-from-godotcon-boston-web-dotnet-prototype/).

{% hint style="warning" %}
Godot 3 web exports are technically supported but not focused on future support.
{% endhint %}

<figure><img src=".gitbook/assets/image (714).png" alt="" width="366"><figcaption></figcaption></figure>

### Export Setting for Godot

Select the desired platform for export. For web exporting, choose `HTML5`. Also **make sure that the export path is set to Build/index.html**. This will lower your packaged build size and rename project exported .html to index.html so VIVERSE can easily find and run the project.&#x20;

<figure><img src=".gitbook/assets/image (713).png" alt="" width="375"><figcaption></figcaption></figure>

### Publish to VIVERSE

From here you can publish the project using the CLI tool just like any other platform.&#x20;

<figure><img src=".gitbook/assets/image (715).png" alt="" width="563"><figcaption></figcaption></figure>

{% stepper %}
{% step %}
#### Log in to VIVERSE platform

A. Open a command prompt and type: **viverse-cli auth login**, then click Enter.

B. Enter VIVERSE **email** and **password**.

C. Confirm login was successful.

<figure><img src=".gitbook/assets/image (8) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### Publish Content

A. To publish content to VIVERSE type the following command with the project path to the project's production build folder: **viverse-cli publish \<path>**, then click Enter.

B. Enter an **Application title** and **Application description**.

C. Confirm the content was published successfully.

<figure><img src=".gitbook/assets/image (693).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### Re-publishing content

A. To re-publish content to VIVERSE when a project is already published, type the following command with the project path to the project's production build folder: **viverse-cli publish \<path>**, then click Enter.

B. Confirm the manifest file is updated.

C. Confirm the content was published successfully.

<figure><img src=".gitbook/assets/image (694).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### Test project

A. Confirm project was published successfully and working properly in VIVERSE by visiting the **URL** that is printed in the **Publish Details**.
{% endstep %}
{% endstepper %}

### General Limitations of Godot and Web Export

List of Godot WebGL rendering issues: [https://github.com/godotengine/godot/issues/66458](https://github.com/godotengine/godot/issues/66458)

