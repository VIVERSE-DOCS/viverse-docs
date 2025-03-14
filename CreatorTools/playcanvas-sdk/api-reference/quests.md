---
description: >-
  This document provides a guide that can be used to setup a quest system in a
  VIVERSE project.
---

# Quests

***

## Quest System

**Create A Quest System**

{% hint style="info" %}
Currently, the Quest system is unavailable for testing in Preview mode and can only be seen after clicking the Create World button in Preview mode.
{% endhint %}

This guide provides instructions for setting up the Quest system. In the sample app, the trigger area is outlined in blue. Once the avatar enters into the trigger area, the Quest system starts. The first task requires the user to click on the red box. Once the user clicks on the red box, the first task is completed and the Quest system updates. The second task requires the user to click on multiple green boxes. Each green box that is clicked adds progress to completing the Quest task. Once all of the green boxes have been clicked, the task is completed and the Quest is completed. If the user clicks on a blue box during the quest, the quest system resets and needs to be triggered again in order to restart the quest.



| <img src="../../.gitbook/assets/image (524).png" alt="" data-size="original">                                    | <img src="../../.gitbook/assets/image (525).png" alt="" data-size="original">                                              | <img src="../../.gitbook/assets/image (526).png" alt="" data-size="original"> |
| ---------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| The avatar enters the trigger area and the Quest system dialog appears.                                          | With the Quest system started, the user clicks on the red box and the first task is completed.                             | When the user clicks each green box, progress is added to the second task.    |
| <img src="../../.gitbook/assets/image (527).png" alt="" data-size="original">                                    | <img src="../../.gitbook/assets/image (528).png" alt="" data-size="original">                                              |                                                                               |
| Once the user clicks on the final green box, the second task is complete and the Quest system dialog disappears. | If the user clicks on a blue box before both tasks are completed, the Quest system resets and needs to be triggered again. |                                                                               |

{% stepper %}
{% step %}
### Open Viverse Scene Settings

A. Click on the Viverse Scene Settings button.

<figure><img src="../../.gitbook/assets/image (514).png" alt="" width="188"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Create the quest

A. Give the quest a name in the **Quest name** field. The text: **Find the boxes!** was added.

B. Give the quest a description in the **Quest description** field. The text: **Click on the boxes that are a specific color.** was added.

C. Create the first task and give the task a description in the **Task description** field. The text: **Click on the red box.** was added.

D. Set the **Task type** to **check**.

E. Create the second task and give the task a description in the **Task description** field. The text: **Click on the green boxes.** was added.

F. Set the **Task type** to **progressBar**.

G. Add the value **4** to the **Progress Steps** field.

<figure><img src="../../.gitbook/assets/image (515).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Create the trigger area that will start the quest

A. Create a new **3D Box** entity.

B. Add a **Collision** component.

C. Adding a material is optional. A transparent material has been added so that the trigger area is visible in play mode.

D. Click the **Edit Viverse Extension** button.

<figure><img src="../../.gitbook/assets/image (516).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Starting the quest

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeTriggerEnter**.

C. Add **local-player** to the **tags to filter** field.

D. Add an **Action** and select **Quest.**

E. In the **selected quest** field, choose **Find the boxes!**

F. In the **quest response** field, choose **startQuest**.

<figure><img src="../../.gitbook/assets/image (517).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Create an object that can be clicked to complete the first task

A. Create a 3D object.

B. Add a **Collision** component.

C. Add a material. Red has been added because this will be the box that will be clicked on to complete the first task.

D. Click the **Edit Viverse Extension** button.

<figure><img src="../../.gitbook/assets/image (518).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Completing the first task

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **NotificationCenterSubscribeEntityPicking**.

C. Add an **Action** and select **Quest.**

D. In the **selected quest** field, choose **Find the boxes!**

E. In the **quest response** field, choose **completeTask**.

F. In the **selected task field,** choose **Click on the red box.**

<figure><img src="../../.gitbook/assets/image (519).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Create multiple objects that can be clicked to complete the second task

A. Create multiple 3D objects.

B. Add a **Collision** component to each object.

C. Add a material. Green has been added because these will be the boxes that will be clicked on to complete the second task.

D. Click the **Edit Viverse Extension** button.

<figure><img src="../../.gitbook/assets/image (520).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Completing the second task

The following steps are completed for all green boxes.

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **NotificationCenterSubscribeEntityPicking**.

C. Add an **Action** and select **Quest.**

D. In the **selected quest** field, choose **Find the boxes!**

E. In the **quest response** field, choose **addTaskProgress**.

F. In the **selected task field,** choose **Click on the green boxes.**

<figure><img src="../../.gitbook/assets/image (521).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Create multiple objects that can be clicked to reset the quest

A. Create multiple 3D objects.

B. Add a **Collision** component to each object.

C. Add a material. Blue has been added because these will be the boxes that will be clicked on to reset the quest.

D. Click the **Edit Viverse Extension** button.

<figure><img src="../../.gitbook/assets/image (522).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Resetting the quest

The following steps are completed for all blue boxes.

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **NotificationCenterSubscribeEntityPicking**.

C. Add an **Action** and select **Quest.**

D. In the **selected quest** field, choose **Find the boxes!**

E. In the **quest response** field, choose **resetQuest**.

<figure><img src="../../.gitbook/assets/image (523).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}
