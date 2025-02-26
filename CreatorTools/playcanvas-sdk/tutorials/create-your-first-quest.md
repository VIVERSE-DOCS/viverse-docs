---
hidden: true
---

# Create Your First Quest

This guide provides instructions for setting up the **Quest** system. In the sample app, the trigger area is outlined in blue. Once the avatar enters into the blue area, the **Quest** system starts. The 1st task informs the avatar to fly. Once the avatar flies and reaches the trigger area in the air, the 1st task is complete and the **Quest** system updates. The 2nd task directs the avatar to fly through the rings. Each ring the avatar flies through adds progress to the **Quest** system. Once the avatar flies through all the rings, the 2nd task is complete and, which completes the quest...

<table data-view="cards"><thead><tr><th></th><th></th><th></th><th></th><th data-hidden data-card-cover data-type="files"></th></tr></thead><tbody><tr><td>The avatar is outside the blue trigger area and the <strong>Quest</strong> system has not started.</td><td></td><td></td><td></td><td><a href="../../.gitbook/assets/1 (1).png">1 (1).png</a></td></tr><tr><td>The avatar has entered the blue trigger area and <strong>Quest</strong> system is visible.<br></td><td></td><td></td><td></td><td><a href="../../.gitbook/assets/2 (1).png">2 (1).png</a></td></tr><tr><td>The 1st task instructs the avatar to fly.</td><td></td><td></td><td></td><td><a href="../../.gitbook/assets/3 (2).png">3 (2).png</a></td></tr><tr><td>Once the avatar enters the trigger area, the 1st task is complete.</td><td></td><td></td><td></td><td><a href="../../.gitbook/assets/4 (1).png">4 (1).png</a></td></tr><tr><td>The avatar passes through the 1st ring and progress is added to the 2nd task.</td><td></td><td></td><td></td><td><a href="../../.gitbook/assets/5 (1).png">5 (1).png</a></td></tr><tr><td>The avatar passes through the final ring and that completes the 2nd task of the quest.</td><td></td><td></td><td></td><td><a href="../../.gitbook/assets/6 (1).png">6 (1).png</a></td></tr></tbody></table>

***

## Create A Quest System

Create A Quest System in VIVERSE Scene Settings

{% stepper %}
{% step %}
### VIVERSE Scene Settings

To configure the quest system, click on the **Viverse Scene Settings** button.

<figure><img src="../../.gitbook/assets/image (308).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### The Quest Section

In the **Viverse Scene Settings** window, expand the **Quest Config** section

A. Click the **plus sign** beside the **Add quest** field.

B. Give the quest a name in the **Quest name** field.

C. Give the quest a description in the **Quest description** field.

D. Click the plus sign beside the **Add Task** field.

E. Give the task a description in the **Task description** field.

F. In this example, when the task is complete, a checkmark will show beside it. Set the **Task type** to check.

G. To add a 2nd task to the same quest, click the plus sign beside the **Add Task** field again. Give the task a name in the **Task description** field.

H. In this example, each time the avatar passes through a ring, additional progress will be added to the completion of the task. Set the **Task type** to **progressBar**.

I. There will be 4 rings that the avatar will pass through to complete the task. Set the **Progress Steps** to **4**.

<figure><img src="../../.gitbook/assets/image (309).png" alt="" width="375"><figcaption></figcaption></figure>


{% endstep %}

{% step %}
### Config The Trigger Entity - Enter Collider

In this example, a trigger area is created and when the avatar enters the trigger area, the Quest System starts. Any object can be used as a trigger, as long as the object has a **Collision** component. Because this specific example creates an empty area that the avatar and other objects can pass through, a simple entity with a box shape is used instead of a 3D object. The entity does not have a **RigidBody** component added. Right-click in the **Hierarchy** and select **New Entity > Entity**.

A. Click the "**Add Component**" button. Select **Physics > Collision** to add a **Collision** component.

B. Resize the collider to the desired size. In this example, the **Half Extents** field has been set to (**2.5, .5, 2.5**) for **X, Y**, **Z**.

C. The entity itself is currently set to Position (0, 0, 0) for X, Y, Z. In this example, the values for Position Offset are set to **(0, -2, 0)** for **X, Y, Z** to lower the collider to the ground.

D. Click the "**EDIT VIVERSE EXTENSION**" button to add the VIVERSE functionality.

<figure><img src="../../.gitbook/assets/image (310).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add Trigger & Action - Enter Collider

To add the **Quest** action functionality that will start the quest

A. Click the **Select plugins** text field and select **TriggerAndAction** in the dropdown menu.

B. Click "+" beside the **Select a module and add** dropdown.

C. Click "+" beside the **selected module** dropdown.

D. Click the **type** dropdown and select a desired trigger. In this example, the **EntitySubscribeTriggerEnter** trigger is selected.

E. Click "+" beside the **selected module** dropdown.

F. Click the **type** dropdown and select **Quest**. For the **Quest** action to work, options for **selected quest** and **quest response** dropdowns need to be selected. In this example, the name of the 1st quest is selected for **selected quest**. Click the **selected quest** dropdown and select **Quest System Example**. Select **startQuest** in the **quest response** dropdown.

<figure><img src="../../.gitbook/assets/image (311).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Config The Trigger Entity - Fly Through Collider

In this example, the avatar will fly above the map and enter into a trigger area hovering above. Right-click in the **Hierarchy** and select **New Entity > 3D > Box.**

A. Click the "**Add Component**" button. Select **Physics > Collision** to add a **Collision** component.

B. Resize the collider to the desired size. In this example, the **Half Extents** field has been set to (**2.5, .5, 2.5**) for **X, Y**, **Z**.

C. Click the "**EDIT VIVERSE EXTENSION**" button to add the VIVERSE functionality.

<figure><img src="../../.gitbook/assets/image (312).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add Trigger & Action - Fly Through Collider

To add the **Quest** action functionality that will complete the first task.

A. Click the **Select plugins** text field and select **TriggerAndAction** in the dropdown menu.

B. Click "+" beside the **Select a module and add** dropdown.

C. Click "+" beside the **selected module** dropdown.

D. Click the **type** dropdown and select a desired trigger. In this example, the **EntitySubscribeTriggerEnter** trigger is selected.

E. Click "+" beside the **selected module** dropdown.

F. Click the **type** dropdown and select **Quest**. For the **Quest** action to work, options for **selected quest**, **quest response** and **selected task** dropdowns need to be selected. In this example, the name of the 1st quest is selected for **selected quest**. Click the **selected quest** dropdown and select **Quest System Example**. Select **completeTask** in the **quest response** dropdown. In the **selected task** field, select the first task that was created.

<figure><img src="../../.gitbook/assets/image (313).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Config The Trigger Entity - Fly Through Multi-Collider

To complete the task, the avatar passes through 4 red rings. A trigger is created in the middle of each ring. Every time the avatar passes through one of these triggers, additional progress is added to the task. Right-click in the **Hierarchy** and select **New Entity > Entity**.

A. Click the "**Add Component**" button. Select **Physics > Collision** to add a **Collision** component.

B. In the **Type** dropdown, select **Cylinder**.

C. Resize the collider to the desired size. In this example, **Radius** has been set to .5.

D. Resize the height to the desired size. In this example, **Height** has been set to 1.

E. Click the "**EDIT VIVERSE EXTENSION**" button to add the VIVERSE functionality.

<figure><img src="../../.gitbook/assets/image (314).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add Trigger & Action - Fly Through Multi-Collider

To add the **Quest** action functionality that will add progress to the completion of the task.

A. Click the **Select plugins** text field and select **TriggerAndAction** in the dropdown menu.

B. Click "+" beside the **Select a module and add** dropdown.

C. Click "+" beside the **selected module** dropdown.

D. Click the **type** dropdown and select a desired trigger. In this example, the **EntitySubscribeTriggerEnter** trigger is selected.

E. Click "+" beside the **selected module** dropdown.

F. Click the **type** dropdown and select **Quest**. For the **Quest** action to work, options for **selected quest**, **quest response** and **selected task** dropdowns need to be selected. In this example, the name of the 1st quest is selected for **selected quest**. Click the **selected quest** dropdown and select **Quest System Example**. Select **addTaskProgress** in the **quest response** dropdown. In the **selected task** field, select the 2nd task that was created.

<figure><img src="../../.gitbook/assets/image (315).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Config The Trigger Entity - Reset

The avatar will pass through a few rings. If the avatar collides with one of the rings, the quest system will reset. A 3D model of the ring is added to the scene. Because this specific example uses a 3D object that the avatar can collide with, a **RigidBody** component needs to be added.

A. Click the "**Add Component**" button. Select **Physics > Collision** to add a **Collision** component. Click the "**Add Component**" button again and select **Physics > Rigid Body** to add the **RigidBody** component.

B. Click the "**EDIT VIVERSE EXTENSION**" button to add the VIVERSE functionality.

<figure><img src="../../.gitbook/assets/image (316).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add Trigger & Action - Reset

To add the **Quest** action functionality that will reset the quest.

A. Click the **Select plugins** text field and select **TriggerAndAction** in the dropdown menu.

B. Click "+" beside the **Select a module and add** dropdown.

C. Click "+" beside the **selected module** dropdown.

D. Click the **type** dropdown and select a desired trigger. In this example, the **EntitySubscribeTriggerEnter** trigger is selected.

E. Click "+" beside the **selected module** dropdown.

F. Click the **type** dropdown and select **Quest**. For the **Quest** action to work, options for **selected quest** and **quest response** dropdowns need to be selected. In this example, the name of the 1st quest is selected for **selected quest**. Click the **selected quest** dropdown and select **Quest System Example**. Select **resetQuest** in the **quest response** dropdown.

<figure><img src="../../.gitbook/assets/image (317).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}
