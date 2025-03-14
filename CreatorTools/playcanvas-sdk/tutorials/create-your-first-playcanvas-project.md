# Create Your First PlayCanvas Project

This guide is a walkthrough for creating a PlayCanvas sample app that can be published to VIVERSE Create.

{% hint style="info" %}
Pre-Upload Configurations:&#x20;

* [Set spawn points](../setting-up-your-playcanvas-project-for-viverse.md#spawn-point)
* [Configure 3D models](../setting-up-your-playcanvas-project-for-viverse.md#collider)
* [Adjust floor and wall settings](../setting-up-your-playcanvas-project-for-viverse.md#setup-the-floor-plan-for-your-avatar-to-walk-and-stand-on)
* [Remove any existing cameras or character controllers ](../setting-up-your-playcanvas-project-for-viverse.md#spawn-point)


{% endhint %}

{% hint style="info" %}
Please follow the provided designer guideline [here](../../optimization.md#id-3d-asset-guide) to meet performance and feasibility specifications. This guide will help ensure your scene is optimized for the VIVERSE platform.&#x20;
{% endhint %}

***

{% stepper %}
{% step %}
### Login & Create A New Project

Login to the PlayCanvas account and click **New** to create a new project.

<figure><img src="../../.gitbook/assets/image (75).png" alt="" width="375"><figcaption></figcaption></figure>


{% endstep %}

{% step %}
### Name The Project

Give the project a name and click **Create**.

<figure><img src="../../.gitbook/assets/image (76).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Edit Project

After the project is created, click the **Editor** button.

<figure><img src="../../.gitbook/assets/image (77).png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Reload To Activate Extension

Click the "**Reload**" button in the browser toolbar to load the extension into the project.

<figure><img src="../../.gitbook/assets/image (78).png" alt="" width="313"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Login To VIVERSE Account

Confirm the extension has been loaded into the project successfully by confirming that the **Login to Viverse** button has been added to the left sidebar of PlayCanvas.

<figure><img src="../../.gitbook/assets/image (79).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Disable The Default Box

Select the **Box** in the **Hierarchy** and uncheck the **Enabled** option to disable the box for now.

<figure><img src="../../.gitbook/assets/image (80).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Creating The Floor Entity

Configure the **Plane** object to become a floor that will prevent the avatar from falling through.

A. Rename the **Plane** to Floor.

B. Set the **Scale** to **(20, 1, 20)** for **X, Y**, **Z**.

C. Click the "**Add Component**" button. Select **Physics > Collision** to add a **Collision** component. Click the "**Add Component**" button again and select **Physics > Rigid Body** to add a **Rigid Body** component.

D. Resize the collider to cover the entire space of the Plane object by setting **Half Extents** field to (**10, .1, 10**) for **X, Y**, **Z**.

E. After the **Floor material** is created in the next step, it can be added here.

<figure><img src="../../.gitbook/assets/image (81).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### **Creating The Floor Material**

Right-click in the **Assets** window and select **New Asset > Material**.Comment.

A. Rename the material to **FloorMaterial**.Comment.

B. Expand the **Diffuse** section and click the **Color** field. Set the values to (**97, 255, 104)** for **r, g, b**. Add it to the **Materials** section on the **Floor** object.Comment.

<figure><img src="../../.gitbook/assets/image (89).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### **Creating The First Wall**

Configure the walls to prevent the avatar from falling off the map. Select the **Box** in the scene and enable it.

A. Rename the **Box** to **Wall1**.

B. Set the values for the **Position** to (**0, 0, 10**) for **X, Y, Z**.

C. Set the values for the **Scale** to **(20, 5, .1)** for **X, Y**, **Z**.

D. Click the "**Add Component**" button. Select **Physics > Collision** to add a **Collision** component. Click the "**Add Component**" button again and select **Physics > Rigid Body** to add a **Rigid Body** component.

E. Resize the collider to cover the entire space of the wall object by setting **Half Extents** to (**10, 2.5, .1**) for **X, Y, Z**.

F. After the **Wall material** is created in the next step, it can be added here.

<figure><img src="../../.gitbook/assets/image (90).png" alt="" width="375"><figcaption></figcaption></figure>


{% endstep %}

{% step %}
### **Creating The Wall Material**

Right-click in the **Assets** window and select **New Asset > Material**.

A. Rename the material to **WallMaterial**.

B. Expand the **Diffuse** section and click the **Color** field. Set the values to (**108, 240, 246**) for **r, g, b**. Add it to the **Materials** section on the **Wall1** object.

<figure><img src="../../.gitbook/assets/image (91).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### **Creating The 2nd Wall**

Select the **Wall1** object and press `Ctrl` + `D` to duplicate the wall.

A. Rename the duplicated object to **Wall2**.

B. Set the values for **Position** to (**0, 0, -10**) for **X, Y, Z**.

<figure><img src="../../.gitbook/assets/image (92).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### **Creating The 3rd Wall**

Select the **Wall2** object and press `Ctrl` + `D` to duplicate the wall.

A. Rename the duplicated object to **Wall3**.

B. Set the values for the **Position** to (**-10, 0, 0**) for **X, Y, Z.**

C. Set the values for the **Rotation** to (**0, 90, 0**) for **X, Y, Z**.

<figure><img src="../../.gitbook/assets/image (93).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### **Creating The 4th Wall**

Select the **Wall3** object and press `Ctrl` + `D` to duplicate the wall.

A. Rename the duplicated object to **Wall4**.

B. Set the values for the **Position** to (**10, 0, 0**) for **X, Y, Z.**

<figure><img src="../../.gitbook/assets/image (94).png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### **Creating A Spawn Point**

To create a spawn point, right-click on the **Root** object and select **New Entity > Entity**. This entity will serve as the initial location where user avatars spawn or respawn. Please make sure your spawn point is above “ground”, to prevent the avatars from falling through the map.

A. Rename the entity to **Spawn Point**.

B. Add the `spawn-point` tag to the **Tags** field.

C. Set the values for the **Position** to (**0, 1, 4**) for **X, Y, Z**.

<figure><img src="../../.gitbook/assets/image (95).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### **Creating The Ball**

To have an object that the avatar can interact with, right-click on the **Root** entity and select **New Entity > 3D > Sphere**.

\
A. Rename the **Sphere** to **Ball**.

B. Set the values for the **Position** to (**0, 1, 0)** for **X, Y, Z**.

C. Set the values for the **Scale** to (**2, 2, 2**) for **X, Y, Z.**

D. Click the "**Add Component**" button. Select **Physics > Collision** to add a **Collision** component. Click the "**Add Component**" button again and select **Physics > Rigid Body** to add a **Rigid Body** component.

E. After the **Ball material** is created in the next step, it can be added here.

F. Change the **Type** dropdown under the **RIGIDBODY** section to **Dynamic**.

<figure><img src="../../.gitbook/assets/image (96).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### **Creating The Ball Material**

Right-click in the **Assets** window and select **New Asset > Material**.

\
A. Rename the material to **BallMaterial**.

B. Expand the **Diffuse** section and click the **Color** field. Set the values to (**244, 139, 139)** for **r, g, b**.. Add it to the **Materials** section on the **Ball** object.

<figure><img src="../../.gitbook/assets/image (97).png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Double Check Camera Is Disabled

Before uploading, ensure any built-in camera settings or character controllers used for testing are disabled or removed.

<figure><img src="../../.gitbook/assets/image (99).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### **Trouble Shooting Advice**

If the 3D models are not registering collisions properly, follow these steps.

A. Click the **Settings** gear icon.

B. Expand the **Physics** section.

C. Click the **Import Ammo** button.

<figure><img src="../../.gitbook/assets/image (100).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}
