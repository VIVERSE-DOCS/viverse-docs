---
description: >-
  This document provides information and guides related to the tools needed to
  migrate a Unity project to PlayCanvas.
---

# Porting from Unity to PlayCanvas

***

## Install Unity Export Tool

**Instructions for using the Unity plugin that will export data, images, models, etc. from Unity and outputs the resources to a folder inside the Unity project**



{% stepper %}
{% step %}
### Install the Unity Export Tool package into the Unity project

To install the Unity Export Tool package, open up the Unity project and go to menu option **Assets > Import Package > Custom Package.**

<figure><img src="../.gitbook/assets/image (381).png" alt="" width="375"><figcaption></figcaption></figure>


{% endstep %}

{% step %}
### Select Unity package to install

Navigate to the location of the **PlayCanvasSceneCreator** Unity package and select it. Click **Open**.

<figure><img src="../.gitbook/assets/image (382).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Review and confirm files for installation

Click **Import**

<figure><img src="../.gitbook/assets/image (384).png" alt="" width="302"><figcaption></figcaption></figure>


{% endstep %}

{% step %}
### Confirm installation was successful

Confirm the PlayCanvas folder has been created in the **Assets** window.

<figure><img src="../.gitbook/assets/image (385).png" alt="" width="287"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Open up window for Unity Export Tool

In Unity project, go to menu option **Window > Playcanvas > Playcanvas Transform Window.**

<figure><img src="../.gitbook/assets/image (386).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Start the export process

Move the **PlaycanvasTransformWindow** tab beside the **Inspector** tab for easy access. Click the **Export Playcanvas** button.

<figure><img src="../.gitbook/assets/image (387).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Locate the generated folder

The **PlaycanvasBuild** folder will be created inside the parent folder of the Unity project. If the Unity project's location is D:\Projects\Unity\Unity\_PlayCanvas\_Export\_Sample, then the **PlaycanvasBuild** folder should be located in D:\Projects\Unity.

<figure><img src="../.gitbook/assets/image (388).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Confirm the export was successful

The files that will be imported into PlayCanvas can be found in the **PlaycanvasBuild\ExportedAssets** folder.

<figure><img src="../.gitbook/assets/image (389).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}



## Install Unity2PlayCanvas Browser Extension

**Instructions for installing the Unity2PlayCanvas browser extension**

Download the Unity2PlayCanvas browser extension. There are two ways install to install the extension. Use only one method of installation (<mark style="color:red;">Important!</mark>).

**Method 1 for installing the Unity2PlayCanvas browser extension using Google Chrome installation method (Recommended).**

{% stepper %}
{% step %}
### Open Chrome browser extensions page

Open chrome browser and go to url: chrome://extensions/
{% endstep %}

{% step %}
### Configure Chrome browser for Developer Mode

Click the **Developer mode button** on the Extensions page to enable Developer Mode

<figure><img src="../.gitbook/assets/image (390).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Install browser extension

Click the **Load unpacked** button to install the extension (select folder ‘{Release file}/Extension’)

<figure><img src="../.gitbook/assets/image (392).png" alt="" width="375"><figcaption></figcaption></figure>


{% endstep %}

{% step %}
### Confirm browser extension was installed successfully

The browser extension will be added to the **All Extensions** list with remove and reload button.

<figure><img src="../.gitbook/assets/image (393).png" alt=""><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}

**Method 2 for installing the Unity2PlayCanvas browser extension using Tampermonkey installation method.**

{% stepper %}
{% step %}
### Download Tampermonkey browser extension&#x20;

The Tampermonkey browser extension can be downloaded here: [https://www.tampermonkey.net](https://www.tampermonkey.net)
{% endstep %}

{% step %}
### Open Tampermonkey dashboard

Open plugin dashboard (this example uses chrome)

<figure><img src="../.gitbook/assets/image (394).png" alt="" width="216"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Install Unity2PlayCanvas browser extension

Drag the Unity2PlayCanvas.js file into the Tampermonkey dashboard. The file location should be ‘{Release file}/Extension/Unity2Playcanvas.user.js’ script.

<figure><img src="../.gitbook/assets/image (395).png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Refresh PlayCanvas editor

Refresh the PlayCanvas editor after adding or updating the script. Delete the old script before updating to new version of the script.
{% endstep %}

{% step %}
### Uninstalling the Unity2PlayCanvas browser extension

In order to uninstall the Unity2PlayCanvas browser extension, open the Tampermonkey dashboard and click the trash can icon.
{% endstep %}
{% endstepper %}





## Import Unity Scene into PlayCanvas

**Instructions for using the PlayCanvas browser extension that will recreate the Unity scene in PlayCanvas editor from the resources generated by the Unity Export Tool**

Integrate the Unity scene into the PlayCanvas editor by creating assets (materials, state graphs) and entities.

{% stepper %}
{% step %}
### Create/select an output folder in PlayCanvas

In PlayCanvas editor, create/select an empty folder under root of Assets folder (<mark style="color:red;">Important!</mark>).
{% endstep %}

{% step %}
### Upload generated files to output folder

Under path ‘{Unity project's Parent folder}\PlaycanvasBuild\ExportAssets’, select all files generated by Unity tool then drag into the folder in PlayCanvas editor.

<figure><img src="../.gitbook/assets/image (370).png" alt="" width="312"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Wait for uploading to complete

<figure><img src="../.gitbook/assets/image (371).png" alt="" width="294"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Start process

Click the Unity2PlayCanvas dropdown to expand/hide the panel and click the ‘Run Progress’ button

<figure><img src="../.gitbook/assets/image (376).png" alt="" width="188"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Wait for process to complete

<figure><img src="../.gitbook/assets/image (373).png" alt="" width="178"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Check for errors in the process

Hover over the error messages to read them and click ‘view’ button to check problem entity or asset.

<figure><img src="../.gitbook/assets/image (379).png" alt="" width="174"><figcaption></figcaption></figure>


{% endstep %}

{% step %}
### Rerun the process if needed

If you want to rerun the process, scroll down in the panel to the bottom and click ‘Clear Created’ button, then wait for progress to delete all created entities and assets

<figure><img src="../.gitbook/assets/image (380).png" alt="" width="174"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}





## Unity Export Tool and Unity2PlayCanvas Browser Extension Release Notes

**Installation Package and Release Notes**

Download:

{% file src="../.gitbook/assets/PlayCanvasSceneCreator_v1.0.10.zip" %}

Please refer to [Unity Export tool](porting-from-unity-to-playcanvas.md#install-unity-export-tool) for usage instructions of the PlayCanvasSceneCreator.unitypackage.

Please refer to [Install Unity2PlayCanvas Browser Extension](porting-from-unity-to-playcanvas.md#install-unity2playcanvas-browser-extension) for instructions on how to use the files under the 'Extension' folder.

We provide a document for querying currently supported components and properties, please see ‘/Document/API/index.html’.

| Release Date | Version | Release Notes                                                                                                                                                                                                                                                                                                                                     |
| ------------ | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 10/18/2024   | 1.0.10  | <p></p><p>add support </p><ul><li>text alignment </li><li>canvas group</li></ul><p>fix </p><ul><li>element use input </li></ul>                                                                                                                                                                                                                   |
| 9/6/2024     | 1.0.9   | <p></p><p>add auto upload tool<br>add support</p><ul><li>ui raw image</li><li>text element wrapLines</li></ul><p>fix</p><ul><li>null case of state motion, scrollbar</li><li>skip hide game object</li><li>scrollview / image / layout on same gameobject</li><li>image mask opacity</li><li>ui entity add layer ui</li></ul>                     |
| 8/16/2024    | 1.0.8   | <p></p><p>add support</p><ul><li>skybox cubemap</li></ul><p>fix</p><ul><li>invalid template name</li><li>material opacity adjust</li><li>material missing map</li><li>export animation on disable gameobject</li><li>light type string</li></ul>                                                                                                  |
| 8/2/2024     | 1.0.7   | <p></p><p>add support </p><ul><li>particle system MainModule </li></ul><p>fix </p><ul><li>model without material </li><li>remove unused component when use template </li></ul>                                                                                                                                                                    |
| 7/19/2024    | 1.0.6   | <p></p><p>fix</p><ul><li>assets file name add id</li><li>improve animation</li><li>improve material reflection</li></ul>                                                                                                                                                                                                                          |
| 6/28/2024    | 1.0.5   | <p></p><p>fix</p><ul><li>Quad </li><li>extension fix find render </li><li>Asset Null handle </li></ul>                                                                                                                                                                                                                                            |
| 6/7/2024     | 1.0.4   | <p></p><p>add components</p><ul><li>text</li></ul><p>add support</p><ul><li>material opacity map</li><li>material skybox reflection</li></ul><p>fix</p><ul><li>layout</li><li>particles color</li><li>mesh renderer active</li><li>mesh load fail</li><li>shader color property</li><li>template id sequence</li><li>extension api fail</li></ul> |
| 5/27/2024    | 1.0.3   | <p></p><p>add components</p><ul><li>Scroll view </li><li>Improve 3d animation file size</li></ul>                                                                                                                                                                                                                                                 |
| 5/3/2024     | 1.0.2   | <p></p><p>add components</p><ul><li>Layer &#x26; tag</li><li>Skybox</li><li>Layout group</li><li>Settings</li></ul>                                                                                                                                                                                                                               |
| 4/8/2024     | 1.0.1   | <p></p><p>add components</p><ul><li>Collider</li><li>Rigidbody</li><li>Particle system</li></ul>                                                                                                                                                                                                                                                  |
| 3/12/2024    | 1.0.0   | first version                                                                                                                                                                                                                                                                                                                                     |
