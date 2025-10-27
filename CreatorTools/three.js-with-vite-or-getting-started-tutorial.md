---
description: >-
  This document provides a guide for creating a sample app in Three.js, building
  the app with Vite and deploying the app to VIVERSE.
---

# ThreeJS Example

***

## Introduction

In this getting-started guide, we will cover the basics of setting up a ThreeJS project and publishing to VIVERSE using [the VIVERSE CLI](https://www.npmjs.com/package/@viverse/cli).

{% include ".gitbook/includes/notice-upload.md" %}

## A. Installing Node.js

{% stepper %}
{% step %}
### Download Node.js

A. Download the latest version of **Node.js (LTS)** from http [https://nodejs.org/en](https://nodejs.org/en).

<figure><img src=".gitbook/assets/image (669).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Automatically install the necessary tools

A. Use the defaults during the installation, but place a checkmark\
in the **Automatically install the necessary tools** checkbox

<figure><img src=".gitbook/assets/image (670).png" alt="" width="304"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Confirm Node.js is installed with at least v22

A. Open a command prompt and type: **node**, then click Enter.

B. Confirm that Node.js is installed when the following message prints in command prompt: **Welcome to Node.js v##.##.##**.

<figure><img src=".gitbook/assets/image (656).png" alt="" width="373"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}

## B. Installing Three.js and making an example project

This guide is a walkthrough for creating an example Three.js project

{% stepper %}
{% step %}
### Create project folder

A. Create a new folder that will contain the project.

<figure><img src=".gitbook/assets/image (671).png" alt="" width="334"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Create the index.html

A. Create the **index.html** page inside the project folder. This can be done by creating a text file, pasting the code and saving it as a **.HTML** page or using an IDE, such as Visual Studio Code.

**index.html**

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>My first three.js app</title>
    <style>
      body { margin: 0; }
    </style>
  </head>
  <body>
    <script type="module" src="/main.js"></script>
  </body>
</html>
```

<figure><img src=".gitbook/assets/image (673).png" alt="" width="371"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Create the main.js

A. Create the **main.js** page inside the project folder. This can be done by creating a text file, pasting the code and saving it as a **.JS** file or using an IDE, such as Visual Studio Code.

**main.js**

```javascript
import * as THREE from 'three';

const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera( 75, window.innerWidth / window.innerHeight, 0.1, 1000 );

const renderer = new THREE.WebGLRenderer();
renderer.setSize( window.innerWidth, window.innerHeight );
renderer.setAnimationLoop( animate );
document.body.appendChild( renderer.domElement );

const geometry = new THREE.BoxGeometry( 1, 1, 1 );
const material = new THREE.MeshBasicMaterial( { color: 0x00ff00 } );
const cube = new THREE.Mesh( geometry, material );
scene.add( cube );

camera.position.z = 5;

function animate() {

  cube.rotation.x += 0.01;
  cube.rotation.y += 0.01;

  renderer.render( scene, camera );

}
```

<figure><img src=".gitbook/assets/image (674).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
## Install Three.js framework

A. Three.js needs to be installed in the project folder. Open command prompt and change directories to your Three.js project.

B. Type: **npm install --save three**.

<figure><img src=".gitbook/assets/image (675).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Confirm Three.js framework is installed

A. Confirm **node\_modules** folder and **package.json** have been added to the directory.

<figure><img src=".gitbook/assets/image (678).png" alt="" width="375"><figcaption></figcaption></figure>

B. Confirm the **three** folder folder and **.package-lock.json** have been added to the **node\_modules** directory.

<figure><img src=".gitbook/assets/image (679).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}

## C. Installing Vite and using it to build your project

{% stepper %}
{% step %}
### Install the build tool Vite

A. If choosing to use **Vite** as the build tool, it needs to be installed in the Three.js project folder also. With command prompt opened and the directory set to your Three.js project, type the command: `npm install --save-dev vite`.

<figure><img src=".gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Confirm Vite has been installed

A. Confirm **Vite** has been installed by checking for additional folders in the **node\_modules** folder.

<figure><img src=".gitbook/assets/image (681).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the Vite build settings

A. Add the Vite build settings to **package.json** file.

**package.json**

```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build"
  },
  "dependencies": {
    "three": "^0.175.0"
  },
  "devDependencies": {
    "vite": "^6.3.2"
  }
}

```

<figure><img src=".gitbook/assets/image (682).png" alt="" width="287"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the vite.config.js file

A. Add the **vite.config.js** file to the root of the project.

**vite.config.js**

```javascript
import { defineConfig } from 'vite';
import path from 'path';

// https://vite.dev/config/
export default defineConfig({
  base: './', // Use relative path as base URL
});
 
```

<figure><img src=".gitbook/assets/image (683).png" alt="" width="369"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Create a development build of the Three.js project

A. To create a development build of the Three.js project, type the following command inside command prompt under the Three.js project directory: **npx vite**.

<figure><img src=".gitbook/assets/image (685).png" alt="" width="369"><figcaption></figcaption></figure>

B. Confirm the development build of the Three.js project was built successfully when Vite provides a **localhost URL** to test.

<figure><img src=".gitbook/assets/image (686).png" alt="" width="320"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Test development build

A. To test a development build of the Three.js project, open the browser and navigate to the URL that was printed in the previous step. In this example, the URL is [http://localhost:5173](http://localhost:5173). Confirm the app works as expected.

<figure><img src=".gitbook/assets/image (687).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Create production build

A. To create a production build of the Three.js project, type the following command inside command prompt under the Three.js project directory: **npx vite build**.

<figure><img src=".gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

B. Confirm the production build of the Three.js project was built successfully by confirming the **dist** folder was created and populated.

<figure><img src=".gitbook/assets/image (5) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}

## D. Installing the VIVERSE CLI

{% stepper %}
{% step %}
### Install the VIVERSE (CLI) command-line tool

A. Inside a command prompt, type: **npm install -g @viverse/cli**, then click Enter. Installing a package with **-g** installs the package globally. The location of globally installed packages depends on your operating system and npm configuration:

* **Windows** : In windows, packages are installed in %APPDATA%\npm\node\_modules.
* **macOS and Linux** : In mac or Linux packages are typically installed in /usr/local/lib/node\_modules or a user-specific directory like \~/.npm-global.

B. Confirm that the command line tool is installed based on screen feedback.

<figure><img src=".gitbook/assets/image (688).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}

## E. Logging in with the VIVERSE CLI

{% stepper %}
{% step %}
### Login to VIVERSE platform

A. Open a command prompt and type: **viverse-cli auth login**, then click Enter.

B. Enter VIVERSE **email** and **password**.

C. Confirm login was successful.

<figure><img src=".gitbook/assets/image (8) (1).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}

## F. Publishing from VIVERSE

{% stepper %}
{% step %}
### Publish content

A. To publish content to VIVERSE type the following command with the project path to the project's production build folder: **viverse-cli publish \<path>**, then click Enter.

B. Enter an **Application title** and **Application description**.

C. Confirm the content was published successfully.

<figure><img src=".gitbook/assets/image (693).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Re-publishing content

A. To re-publish content to VIVERSE when a project is already published, type the following command with the project path to the project's production build folder: **viverse-cli publish \<path>**, then click Enter.

B. Confirm the manifest file is updated.

C. Confirm the content was published successfully.

<figure><img src=".gitbook/assets/image (694).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Test project

A. Confirm project was published successfully and working properly in VIVERSE by visiting the **URL** that is printed in the **Publish Details**.

<figure><img src=".gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}
