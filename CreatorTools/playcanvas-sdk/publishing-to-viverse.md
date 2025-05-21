# Publishing to VIVERSE

***

{% hint style="info" %}
**Definition:**&#x20;

A Scene is the blueprint of your virtual space registered in VIVERSE Create. One Scene can generate multiple identical World.

A World is your designated virtual space in VIVERSE Create, accessible via a unique URL.
{% endhint %}

## VRAM Classification and Performance Recommendations

To ensure consistent performance and platform compatibility, our system classifies content based on **VRAM usage per world** and provides platform-specific **performance recommendations**. Developers should optimize their content to target the desired range of supported devices.

#### VRAM Usage Categories

The system automatically assigns a **performance boundary level** based on the amount of VRAM your world uses:

| Boundary Level | VRAM Usage          |
| -------------- | ------------------- |
| **Low**        | ≤ 500 MB            |
| **Medium**     | 500 MB and ≤ 800 MB |
| **High**       | 800 MB and ≤ 2 GB   |
| **Very High**  | 2 GB                |

{% hint style="warning" %}
Worlds marked **Very High** will trigger a **warning popup** for users, indicating that the content is intended for high-end PCs only.
{% endhint %}

#### Platform Recommendations

Each boundary level maps to a set of recommended platforms, reflecting performance expectations:

| Boundary Level | Recommended Platforms                  |
| -------------- | -------------------------------------- |
| **Low**        | PC / iOS / Android / HMD (VR Headsets) |
| **Medium**     | PC / Android                           |
| **High**       | PC                                     |
| **Very High**  | PC (High-End only, with warning popup) |

#### Developer Guidance

* **Targeting Wider Compatibility**: Aim to keep VRAM usage **under 500 MB** to support the broadest range of devices.
* **Optimizing for Android or iOS**: Stay within **Low** or **Medium** boundary levels to ensure mobile device compatibility.
* **High-End Visuals**: Worlds exceeding **800 MB VRAM** should be optimized for **PC-only** audiences.
* **Exceeding 2 GB VRAM**: Consider splitting large scenes, using LODs, or reducing texture resolutions to avoid user warnings.

For best results, monitor your world’s VRAM usage regularly during development and test on target platforms.





## The Scene Preview

This guide is a walkthrough for uploading and publishing a PlayCanvas project to VIVERSE Create.

{% stepper %}
{% step %}
### Log in to VIVERSE

{% hint style="warning" %}
Make sure to use the same email address as your PlayCanvas account.
{% endhint %}

<figure><img src="../.gitbook/assets/image (332).png" alt="" width="287"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Open The Publish/Download Menu

Click the **Publish / Download** to open up the **Builds** window.

<figure><img src="../.gitbook/assets/image (333).png" alt="" width="287"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Publish

On the **Builds** window, select the **Builds & Publish tab** on the left and click the **Publish to Viverse Create** button to upload the project. This may take a few minutes and a progress bar will show the upload status

<figure><img src="../.gitbook/assets/image (334).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Preview The Scene

After the project has been successfully published, click the **Preview** button to view the project.

<figure><img src="../.gitbook/assets/image (335).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Go to Scene&#x20;

Use the preview URL to verify the scene on VIVERSE Create. If necessary, return to the PlayCanvas Editor to make changes then re-publish the project.

<figure><img src="../.gitbook/assets/image (336).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}

## Creating A World URL

This guide is a walkthrough creating World from Scene.

***

{% stepper %}
{% step %}
### Create World

After confirming everything is set in your preview URL, click the "**Create World**" button.

<figure><img src="../.gitbook/assets/image (337).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Name The World

Give the World a name and click the "**Create**" button to publish it to the VIVERSE Create platform. The resulting URL is the public link to the World.

<figure><img src="../.gitbook/assets/image (338).png" alt="" width="344"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Attain The World Link

The world can be accessed by copying and pasting the URL into the browser.

<figure><img src="../.gitbook/assets/image (339).png" alt="" width="368"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Go To Your World

The World can also be accessed by clicking the "**Go to world**" button.

<figure><img src="../.gitbook/assets/image (340).png" alt="" width="397"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Tutorial in World

The keyboard and mouse controls will be displayed when the World has opened.

<figure><img src="../.gitbook/assets/image (341).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### You're All Set!

The World should be viewable and the avatar can be used to interact with the world.

<figure><img src="../.gitbook/assets/image (342).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Go To VIVERSE Create Site

The World can be accessed from the VIVERSE Create site [here](https://world.viverse.com/). Once logged into VIVERSE Create, click the avatar.

<figure><img src="../.gitbook/assets/image (343).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Go To My Worlds

Click "**My Worlds"** to display all the Worlds created with this account.

<figure><img src="../.gitbook/assets/image (344).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Your World List

On the **Worlds** tab, the World that was created should be available.

<figure><img src="../.gitbook/assets/image (345).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}
