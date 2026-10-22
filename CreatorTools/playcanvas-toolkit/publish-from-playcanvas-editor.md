---
description: >-
  Initialize a PlayCanvas project with the VIVERSE Toolkit v4 and publish it to
  VIVERSE directly from the PlayCanvas Editor, without downloading a build.
hidden: true
---

# Publish to VIVERSE from the PlayCanvas Editor

The VIVERSE PlayCanvas Toolkit can publish your project to VIVERSE from inside the PlayCanvas Editor. This page shows how to initialize a project, test it locally, and publish it without downloading and re-uploading a build.

***

{% hint style="danger" %}
**Draft: not yet verified against Toolkit v4.2.1.** The steps in [Publish from the Editor](#publish-from-the-editor) are reconstructed from an earlier version of this guide. Confirm each button name and screen before you remove this notice and unhide the page.
{% endhint %}

## Before you start

You need:

* The VIVERSE PlayCanvas Toolkit v4 or later installed in Chrome. See [Toolkit Setup](toolkit-setup.md).
* A PlayCanvas account and a VIVERSE account that use the same email address. The Toolkit can fail to sign in when the emails differ.

## Initialize your project

Initializing adds the VIVERSE Framework to your project and creates a matching project in VIVERSE Studio. You do this once per PlayCanvas project.

{% stepper %}
{% step %}
### Open your project and enable the Toolkit

Open an existing project in the PlayCanvas Editor, or create a new one. Then click the **Extensions** icon in the Chrome toolbar and select **VIVERSE PlayCanvas Toolkit**.
{% endstep %}

{% step %}
### Sign in to VIVERSE

Select **Log In to VIVERSE** in the left-hand toolbar and complete the VIVERSE sign-in.
{% endstep %}

{% step %}
### Create a VIVERSE world

Select **VIVERSE PlayCanvas Toolkit Settings** in the left-hand toolbar, then select **Create New World**. Wait for the Toolkit to add its scripts and assets to your project. They're added under the `.viverse` folder.

Creating a world also creates a project for it in [VIVERSE Studio](https://studio.viverse.com/) under your account. Your published builds go to that project.
{% endstep %}

{% step %}
### Add something to stand on

Add an entity with a **Collision** component and a static **Rigidbody**, such as a floor plane. Adding physics imports the Ammo physics library, which the VIVERSE player system needs to move around.
{% endstep %}

{% step %}
### Test locally

Select **Launch** in the PlayCanvas Editor. The local session runs the VIVERSE Framework, so you can test movement, avatars, and multiplayer audio before you publish.
{% endstep %}
{% endstepper %}

## Publish from the Editor

Publishing from the Editor builds your project and uploads it to its VIVERSE Studio project in one step. New uploads start in staging, where only you and your collaborators can see them.

{% stepper %}
{% step %}
### Open the Builds window

Select **Publish / Download** in the left-hand toolbar to open the **Builds** window.
{% endstep %}

{% step %}
### Publish to VIVERSE

Select **Publish to VIVERSE** and wait for the upload to finish. A progress bar shows the upload status, and it can take a few minutes for larger projects.
{% endstep %}

{% step %}
### Preview your world

When the upload finishes, select **Preview** to open your world on VIVERSE in a new tab. Check that everything works the way it did in your local session.
{% endstep %}

{% step %}
### Keep iterating

Return to the PlayCanvas Editor, make changes, and publish again. Each publish replaces the build in your VIVERSE Studio project.
{% endstep %}
{% endstepper %}

## Make your world public

Publishing from the Editor puts your world in staging. To make it public, open your project in [VIVERSE Studio](https://studio.viverse.com/), complete its settings, and select **Submit for Review**. For what review checks and how long it takes, see [How content review works on VIVERSE](../content-review.md).

After your world is indexed, each new publish goes through the same review before it goes live.

## Export and upload instead

If you'd rather manage builds yourself, you can still download a build and upload it to VIVERSE Studio:

1. In the PlayCanvas Editor, open **Publish / Download** and create a new build.
2. Download the build's `.zip` file.
3. Open [VIVERSE Studio](https://studio.viverse.com/) and upload the `.zip` to the project the Toolkit created for your world. See [How to Publish](../how-to-publish.md).

{% include "../.gitbook/includes/factory-reset-playcanvas-....md" %}
