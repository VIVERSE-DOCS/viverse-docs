---
description: This page details how to publish an iframe of your game on VIVERSE.
hidden: true
---

# iFraming Your Project on VIVERSE

***

{% hint style="warning" %}
**If this is your first time using VIVERSE, please create a VIVERSE account:** [**https://worlds.viverse.com/**](https://worlds.viverse.com/profile)
{% endhint %}

## 1. Create a blank index.html.zip file

Using VSCode or other development environment, create a file titled `index.html` and paste the following code snippet...

<pre class="language-html"><code class="lang-html">&#x3C;!DOCTYPE html>
&#x3C;html lang="en">
    &#x3C;head>
        &#x3C;meta charset="UTF-8">
        &#x3C;title>VIVERSE Project&#x3C;/title>
        &#x3C;style>
        html, body {
            margin: 0;
            padding: 0;
            height: 100%;
            overflow: hidden;
        }
        iframe {
            width: 100%;
            height: 100%;
            border: none;
        }
        &#x3C;/style>
    &#x3C;/head>
    &#x3C;body>
<strong>        &#x3C;iframe src="https://yourURL..." allowfullscreen>&#x3C;/iframe>
</strong>    &#x3C;/body>
&#x3C;/html>
</code></pre>

Replace `https://yourURL...` with the url that you would like to embed as an iframe. Once complete, save your file and compress the index.html file into a zip file.

## 2. Upload the file to VIVERSE Studio

### A. Visit [https://studio.viverse.com/upload](https://studio.viverse.com/upload)

The upload page allows you to manage and add specific content to your VIVERSE account. For each world, you can manage its version status, visibility setting, and permissions.

<figure><img src=".gitbook/assets/Screenshot 2025-08-26 at 3.54.24 PM.png" alt="" width="563"><figcaption></figcaption></figure>

### B. Select "Create New World"

Enter a title and description for your world (these can be edited later), acknowledge the terms of use, and select "Create".

<figure><img src=".gitbook/assets/Screenshot 2025-08-26 at 3.57.01 PM.png" alt="" width="563"><figcaption></figcaption></figure>

### C. Upload Content

Once your world has been created, navigate to the "Content Version" tab and select the index.html.zip file you compressed earlier.

<figure><img src=".gitbook/assets/Screenshot 2025-08-26 at 3.58.32 PM.png" alt="" width="563"><figcaption></figcaption></figure>

### D. View and Test using a Preview

After uploading, your content will be viewable in preview mode only to your account. You may use "Guest Preview" to generate a temporary url token to share with others for 10 minutes at a time. While not usually required, you may access additional settings using the "Apply iframe settings" to enable features like XR spatial tracking.

<figure><img src=".gitbook/assets/Screenshot 2025-08-26 at 4.03.38 PM.png" alt="" width="563"><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/Screenshot 2025-08-26 at 4.01.26 PM.png" alt="" width="563"><figcaption></figcaption></figure>

### E. Publishing Edits

If you need to re-publish versions, you will have to re-upload and merge content versions if you have already published your world.

<figure><img src=".gitbook/assets/Screenshot 2025-08-26 at 4.15.00 PM.png" alt="" width="563"><figcaption></figcaption></figure>

## 3. Publish Your World

After testing your world, you can submit it for approval and publish it for discovery to VIVERSE users.

### A. Submit For Review

Select "Submit for Review" twice to submit the project to the moderation team. Only check the box for iframe support if you would like to iframe the VIVERSE url on other webpages. It typically takes less than 24 hours to review, but you may also DM MikeMorran@HTC on the [VIVERSE Discord](https://discord.gg/viversecreators) to get quick approval.&#x20;

<figure><img src=".gitbook/assets/Screenshot 2025-08-26 at 4.05.14 PM.png" alt="" width="563"><figcaption></figcaption></figure>

### B. Configure World Settings

Navigate to [https://worlds.viverse.com/](https://worlds.viverse.com/profile) and scroll down to the "My Worlds" section. Select the kebab menu next to the world and select "World Settings" to navigate to the settings page.

<figure><img src=".gitbook/assets/Screenshot 2025-08-26 at 4.20.01 PM.png" alt="" width="563"><figcaption></figcaption></figure>

On the worlds settings page, complete the following...

* Set the world's access to Public
* Finalize your world's name
* Finalize your world's description
* Upload and engaging thumbnail
* Select genres
* Select the proper device filers
* Manage the position of the VIVERSE logo overlay
* (optional) upload media that will display if your world is accessed on unsupported devices

## Important Recommendations

### Thumbnails and Discovery

VIVERSE world's with engaging thumbnails typically attract the most traffic and viewers. Here are some examples of engaging thumbnails that have performed well on the platform.

<figure><img src=".gitbook/assets/Screenshot 2025-08-26 at 4.26.18 PM.png" alt="" width="375"><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/Screenshot 2025-08-26 at 4.26.39 PM.png" alt="" width="375"><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/Screenshot 2025-08-26 at 4.27.16 PM.png" alt="" width="375"><figcaption></figcaption></figure>

### CORS/COOP/COEP Headers

If your project needs to access an external API, the VIVERSE team will have to configure this for you manually. DM Michael on discord or email michael\_morran@htc.com and we will configure this for you!
