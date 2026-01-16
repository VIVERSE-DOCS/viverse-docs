---
hidden: true
noIndex: true
---

# \[WIP] Starting with PlayCanvas Toolkit

{% hint style="success" %}
Working notes:

* Covering v3 for now, adapt for v4 later
* ~~Remove: PlayCanvas account creation section~~ \[✓]
* ~~Add / Refactor: PlayCanvas Extension setup (Install + Factory Reset)~~ \[✓]
* ~~Add / Refactor: Your first PlayCanvas project (+ video)~~ \[✓]
* ~~Add / Refactor: Publishing to Viverse — download ZIP / publish via Extension~~ \[✓]
* ~~Move: Changelog to a separate pag~~e \[✓]
{% endhint %}

## Install PlayCanvas Extension

> PlayCanvas Extension comes with a variety of helpful scripts and no-code tools, and will assist you with creating and publishing your projects to VIVERSE

{% columns %}
{% column width="66.66666666666666%" %}
{% stepper %}
{% step %}
#### Get the latest Extension from VIVERSE

* [Download](wip-starting-with-playcanvas-toolkit.md#download-the-latest-extension-version) the latest version of Playcanvas Extension
* Unzip download file to some folder on your computer
{% endstep %}

{% step %}
#### Navigate to Chrome Extensions manager

* Click Extensions icon in Chrome toolbar and open the Extensions Popup
* Click Manage Extensions at the bottom and open the Extensions Manager tab
{% endstep %}

{% step %}
#### Install the Extension

* Enable Developer Mode at the top right corner
* Click Load Unpacked and select the folder with unpacked Extension
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

## Install PlayCanvas Extension (no stepper)

> PlayCanvas Extension comes with a variety of helpful scripts and no-code tools, and will assist you with creating and publishing your projects to VIVERSE

1. Download the latest version of PlayCanvas Extension for Chrome
2. Unzip download file to some folder on your computer
3. Click Extensions icon in Chrome toolbar and open the Extensions Popup
4. Click Manage Extensions at the bottom and open the Extensions Manager tab
5. Enable Developer Mode at the top right corner
6. Click Load Unpacked and select the folder with unpacked Extension
7. Verify the Extension is now present in Extensions Manager tab

```
>>> Could we prepare a short illustrative video instead of 7 images
```

<div align="left" data-full-width="false"><figure><img src="../.gitbook/assets/1-1 (1).png" alt="" width="563"><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/2-2 (1).png" alt="" width="375"><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/3-1 (1).png" alt="" width="375"><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/4 (2).png" alt=""><figcaption></figcaption></figure></div>

<div align="center"><figure><img src="../.gitbook/assets/5 (1) (1).png" alt="" width="188"><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/6 (2).png" alt="" width="375"><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/7 (1).png" alt="" width="246"><figcaption></figcaption></figure></div>

## Create your first project

> Explore PlayCanvas Extension essentials by creating your first World and publishing it to VIVERSE

{% columns %}
{% column width="66.66666666666666%" %}
{% stepper %}
{% step %}
#### Initialize new project

* Create a new PlayCanvas project
* Login into VIVERSE via Extension
* Delete default **Camera** in Scene Hierarchy
{% endstep %}

{% step %}
#### Create the Floor entity

* Select the **Plane** entity and rename it **Floor**
* To increase the size of the **Floor,** change the **Scale** to **(20, 1, 20)**
* To allow the players to collide with the **Floor**, add a **Collision** component
* To increase the size of the **Collision** component so that it covers the entire **Floor**, change **Half Extents** to **(10, .1, 10)**
* To prevent the players from falling through the **Floor,** add a **Rigidbody** component
* Click the **Import Ammo** button
{% endstep %}

{% step %}
#### Assign Floor material

* Create a new folder and name it **Materials**
* Inside the **Materials** folder, create a new material and name it **FloorMaterial**
* Expand the **Diffuse** section and click the **Color** field
* For the **Color**, change the values to **(97, 255, 104)**
* Add the **FloorMaterial** to the **Material** slot on the **Floor** entity
{% endstep %}

{% step %}
#### Create the Wall entity

* Select the **Box** entity and rename it to **Wall1**
* To move the wall to the side, change the **Position** to **(0, 0, 10)**
* To increase the size of the wall, change **Scale** to **(20, 5, .1)**
* To allow the players to collide with the wall, add a **Collision** component
* To increase the size of the **Collision** component so that it covers the entire wall, change **Half Extents** to **(10, 2.5, .1)**
* To prevent the players from going through the wall, add a **Rigidbody** component.
{% endstep %}

{% step %}
#### Assign Wall material

* Inside the **Materials** folder, create a new material and name it **WallMaterial**
* Expand the **Diffuse** section and click the **Color** field
* For the **Color**, change the values to **(108, 240, 246)**
* Add the **WallMaterial** to the **Material** slot on the **Wall1** entity
{% endstep %}

{% step %}
#### Create remaining walls

* Select **Wall1** and click `CTRL + D` to duplicate the wall. Rename the wall to **Wall2**
* Move the wall to the opposite side by changing **Position** to **(0, 0, -10)**
* Select **Wall2** and click `CTRL + D` to duplicate the wall. Rename the wall to **Wall3**
* To place the wall in the correct space, change the **Position** to **(-10, 0, 0)**
* To have the wall face the correct direction, change the **Rotation** to **(0, 90, 0)**
* Select **Wall3** and click `CTRL + D` to duplicate the wall. Rename the wall to **Wall4**
* Move the wall to the opposite side by changing **Position** to **(10, 0, 0)**
* Assign **WallMaterial** to newly created walls
{% endstep %}

{% step %}
#### Create Ball entity

* Add a **3D Sphere** to the scene. Rename it to **Ball**
* Raise the **Ball** above the ground by changing **Position** to **(0, 1, 0)**
* To allow the players to collide with the **Ball**, add a **Collision** component
* To change the collider shape to be the same shape as the **Ball,** change **Type** to **Sphere**
* To prevent players from passing through the **Ball**, add a **Rigidbody** component
* Since the **Ball** will be moving, change the **Type** to **Dynamic**
* Decrease the **Friction** to **.1** so that the **Ball** doesn't lose as much velocity when coming in contact with other entities
* Increase the **Restitution** to **1** to maximize the bounciness of the **Ball**.
{% endstep %}

{% step %}
#### Assign Ball material

* Inside the **Materials** folder, create a new material and name it **BallMaterial**
* Expand the **Diffuse** section and click the **Color** field
* For the **Color**, change the values to **(244, 139, 139)**
* Add the **BallMaterial** to the **Material** slot on the **Ball** entity
{% endstep %}

{% step %}
#### Create a Spawn Point

* Add a new **Entity** to the scene. Rename the entity to **SpawnPoint**
* Add `spawn-point` to the **Tags** field
* To have the spawn point above the ground and close to the wall, change the **Position** to **(0, 1, 8)**
* To change the direction of the **SpawnPoint** so that the player will face the ball when spawned in, change **Rotation** to **(0, 180, 0)**
{% endstep %}

{% step %}
### Ready for publishing

* Now you're ready to publish your project to the VIVERSE!
{% endstep %}
{% endstepper %}
{% endcolumn %}

{% column width="33.33333333333334%" %}
<figure><img src="../.gitbook/assets/2-2.jpg" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/2-3.jpg" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/2-4.jpg" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/2-5.jpg" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/2-6.jpg" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/2-7.jpg" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/2-9.jpg" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/2-10.jpg" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/2-11.jpg" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/2-12.jpg" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/2-14.jpg" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/2-15.jpg" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/2-16.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

{% embed url="https://www.youtube.com/watch?v=w3MFHo4v69E" %}

## Publish your project

> This guide is a walkthrough for uploading and publishing a PlayCanvas project to VIVERSE Create

{% columns %}
{% column width="66.66666666666666%" %}
{% stepper %}
{% step %}
#### Open PlayCanvas Publish Menu

* Click the **Publish / Download** to open up the **Builds** window
{% endstep %}

{% step %}
#### Upload your project to the VIVERSE

* In the **Builds** window, select the **Builds & Publish tab** on the left and click the **Publish to Viverse Create** button to upload the project. This may take a few minutes and a progress bar will show the upload status
{% endstep %}

{% step %}
#### Preview your project live

* After the project has been successfully published, click the **Preview** button to view the project
* Use the preview URL to verify the scene on VIVERSE Create. If necessary, return to the PlayCanvas Editor to make changes then re-publish the project
{% endstep %}

{% step %}
#### Create a new World

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
<div><figure><img src="../.gitbook/assets/image (332).png" alt="" width="287"><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/image (333).png" alt="" width="287"><figcaption></figcaption></figure></div>

<figure><img src="../.gitbook/assets/image (334).png" alt="" width="375"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (335).png" alt="" width="375"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (337).png" alt="" width="375"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (339).png" alt="" width="368"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (341).png" alt="" width="375"><figcaption></figcaption></figure>


{% endcolumn %}
{% endcolumns %}



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
