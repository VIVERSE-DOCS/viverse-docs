---
description: This document provides a guide for publishing JavaScript projects to VIVERSE
---

# Publishing to VIVERSE

* Prequisites: You must have installed the VIVERSE CLI Module (link back to previous page if they haven't)
* Description of the manifest.json
* Publishing to VIVERSE
  * Publishing from the command line
  * What are the success messages when publishing has completed
  * Finding your published world on VIVERSE.com & adjusting world settings
* Troubleshooting
  * Using relative vs. absolute paths

***

## Pre-Requisites

* VIVERSE CLI module must be installed. Instructions can be found [here](installing-the-viverse-cli-module.md).

## Manifest.json

{% hint style="warning" %}
**Important:** When you publish content, the CLI automatically creates a `.viverse-cli/manifest.json` file in your target path. This file contains essential metadata about your content, including its deployment ID and URL.

If you need to update the same content later (especially in CI/CD pipelines), **make sure to preserve this folder**. The manifest file is critical for tracking content versions and ensuring proper updates rather than creating new content each time.
{% endhint %}

**manifest.json**

```json
{
  "name": "my-three-project",
  "description": "Test Viverse-cli project",
  "id": "##########",
  "accountId": "########-####-####-####-############",
  "deploymentUrl": "https://create.viverse.com/SEzTaQg"
}
```



## Publishing content to VIVERSE

{% stepper %}
{% step %}
### Login to VIVERSE platform

A. Open a command prompt and type: **viverse-cli auth login**, then click Enter.

B. Enter VIVERSE **email** and **password**.

C. Confirm login was successful.

<figure><img src="../.gitbook/assets/image (5) (1).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Publish content

A. To publish content to VIVERSE type the following command with the project path to the project's production build folder: **viverse-cli publish \<path>**, then click Enter.

B. Enter an **Application title** and **Application description**.

C. Confirm the content was published successfully.

<div align="center"><figure><img src="../.gitbook/assets/image (6) (1).png" alt="" width="375"><figcaption></figcaption></figure></div>
{% endstep %}

{% step %}
### Re-publishing content

A. To re-publish content to VIVERSE when a project is already published, type the following command with the project path to the project's production build folder: **viverse-cli publish \<path>**, then click Enter.

B. Confirm the manifest file is updated.

C. Confirm the content was published successfully.

<figure><img src="../.gitbook/assets/image (3) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Test Content

A. Confirm project was published successfully and working properly in VIVERSE by visiting the **URL** that is printed in the **Publish Details**.

<figure><img src="../.gitbook/assets/image (697).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}



## Adjusting World Settings

{% stepper %}
{% step %}
### Open My Worlds

A. Visit **Viverse.com** and login. After logging in, click on your avatar.

B. Select **My Worlds.**

<figure><img src="../.gitbook/assets/image (699).png" alt="" width="375"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (703).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Open World Settings for the published world

A. Locate the world that was published and click the **three dots** icon underneath the world.

B. Select **World Settings**.

<figure><img src="../.gitbook/assets/image (701).png" alt="" width="375"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (705).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Adjust World Settings

<figure><img src="../.gitbook/assets/image (706).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}



## Find your published world on VIVERSE.COM

{% stepper %}
{% step %}
### Make the world public

A. Open up **World Settings** and change the **Access** to **Public**.

<figure><img src="../.gitbook/assets/image (707).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Locate published world on Viverse.com

A. Go to **Viverse.com**. Scroll down and click on **View All Worlds**.

B. Type the name of world in the **search bar**.

C. Select the world in the page.

<figure><img src="../.gitbook/assets/image (708).png" alt="" width="375"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (709).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}

## Troubleshooting

<details>

<summary>Using relative vs. absolute paths in build files</summary>

Here's some code used in a vite.config.js build file. In Node.js, `__dirname` is a global variable that represents the absolute path of the directory containing the currently executing JavaScript file. Deploying projects to VIVERSE with absolute paths will cause errors and may prevent your project from operating correctly. Using relative paths is required.

```javascript
export default defineConfig({
  // base: "/assets/games/b16eab3d/202504021539/",
  plugins: [vue(), mkcert()],
  resolve: {
    alias: {
      "#game": path.resolve(__dirname, "./src/game"),
      "#components": path.resolve(__dirname, "./src/components"),
      "#assets": path.resolve(__dirname, "./src/assets"),
    },
  },
  server: {
    host: true,
  },
});
```

</details>

## Checking VIVERSE authentication status

{% stepper %}
{% step %}
### Check authentication status

A. Type the following command inside a command prompt: **viverse-cli auth status**, then click Enter.

B. Confirm the status authentication status.

<figure><img src="../.gitbook/assets/image (6) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}

## Viewing VIVERSE application list

{% stepper %}
{% step %}
### View list of applications published under the current account logged into VIVERSE

A. Type the following command inside command prompt: `viverse-cli app list`, the click Enter.

B. Confirm the list of published applications is printed.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}

## Logging out of VIVERSE

{% stepper %}
{% step %}
### Logout of VIVERSE platform

A. Type the following command inside command prompt: **viverse-cli auth logout**, then click Enter.

B. Confirm the logout was successful.

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}
