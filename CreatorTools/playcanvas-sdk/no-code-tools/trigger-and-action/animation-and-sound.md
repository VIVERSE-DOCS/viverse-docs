---
description: >-
  This document provides several guides that can be used to setup event
  listeners for animations, control animations and control audio in a VIVERSE
  project.
---

# Animation & Sound

## EntityPlayAnimation

**Create An Action That Plays An Animation**

This guide provides instructions for setting up the **EntityPlayAnimation** action. In the sample app, once the avatar enters the trigger area, the character switches to the dancing animation state. When the avatar leaves the trigger area, the character switches back to the idle animation state.

| <img src="../../../.gitbook/assets/image (49).png" alt="" data-size="original">    | <img src="../../../.gitbook/assets/image (50).png" alt="" data-size="original">                |
| ---------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| The character is in the idle state when the avatar is outside of the trigger area. | The character switches to the dancing animation state when the avatar enters the trigger area. |

In this example, a trigger area is created and when triggered, an action plays an animation. Any object can be used as a trigger, as long as the object has a collision component.&#x20;

{% stepper %}
{% step %}
### Create the animation state graph

A. Create an **Animation State Graph** and rename the initial state to **Idle**.

B. Create an additional animation state and name it **Dancing**.

<figure><img src="../../../.gitbook/assets/image (51).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Configure 3D model for animation

A. Add a 3D model to the scene.

B. Add an **Anim** component.

C. Add the **Animation State Graph**.

D. Add the **Idle** animation.

E. Add the **Dancing** animation.

<figure><img src="../../../.gitbook/assets/image (52).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Create a trigger area

A. Create a new **3D Box** entity.

B. The **Collision** component is not required for **EntityPlayAnimation** to work. The **Collision** component is required for the **EntitySubscribeTriggerEnter** trigger that will be used in this example.

C. Adding a material is optional. A transparent material has been added so that the trigger area is visible in play mode.

D. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (53).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the EntityPlayAnimation action for the dancing animation state

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeTriggerEnter**.

C. Add **local-player** to the **tags to filter** field.

D. Add an **Action** and select **EntityPlayAnimation.**

E. Add the name of an animation state to the **animate state to play** field. In this example, the animation state name is **Dancing**.

F. Add the 3d model to the **pick up specify execution entity** field.

<figure><img src="../../../.gitbook/assets/image (54).png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the EntityPlayAnimation action for the Idle animation state

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeTriggerLeave**.

C. Add **local-player** to the **tags to filter** field.

D. Add an **Action** and select **EntityPlayAnimation.**

E. Add the name of an animation state to the **animate state to play** field. In this example, the animation state name is **Idle**.

F. Add the 3d model to the **pick up specify execution entity** field.

<figure><img src="../../../.gitbook/assets/image (55).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}





## EntitySubscribeAnimationStart

**Create A Trigger Based On When An Animation Starts**

This guide provides instructions for setting up the **EntitySubscribeAnimationStart** trigger. In the sample app, when the avatar enters the blue trigger area, the character’s dancing animation begins to play. The dancing animation has an animation event added and the animation event is triggered whenever the animation starts to play.

| <img src="../../../.gitbook/assets/image (500).png" alt="" data-size="original"> | <img src="../../../.gitbook/assets/image (501).png" alt="" data-size="original">                                                                      |
| -------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| Before the character’s dancing animation begins.                                 | When the character’s dancing animation begins, the animation event is triggered and text is displayed to show that the animation event was triggered. |

In the **EntityPlayAnimation** example, a 3D model was added, an animation state graph was created, animation states were created and a trigger area was created to initiate the animations. In this example, an animation event is created for the start of the animation.

{% stepper %}
{% step %}
### Open animation file that will have the animation event

A. Click on the animation file to open up the properties.

<figure><img src="../../../.gitbook/assets/image (502).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Create animation event

A. Create an event, add value **0** to the **time** field and **start** to the **name** field.

<figure><img src="../../../.gitbook/assets/image (503).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the EntitySubscribeAnimationStart trigger

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeAnimationStart**.

C. Add an **Action** and select **EntityEnableById.**

D. For the **EntityEnableById** action to work, an object needs to be added that will be enabled. In this example, the **AnimationStartText** object is added.

<figure><img src="../../../.gitbook/assets/image (504).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}







## EntitySubscribeAnimationEnd

**Create A Trigger Based On When An Animation Ends**

This guide provides instructions for setting up the **EntitySubscribeAnimationEnd** trigger. In the sample app, when the avatar enters the blue trigger area, the character’s dancing animation begins to play. The dancing animation has an animation event added and the animation event is triggered whenever the animation stops playing.

| <img src="../../../.gitbook/assets/image (509).png" alt="" data-size="original"> | <img src="../../../.gitbook/assets/image (506).png" alt="" data-size="original">                                                                    |
| -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| The character’s dancing animation has started.                                   | When the character’s dancing animation ends, the animation event is triggered and text is displayed to show that the animation event was triggered. |

In the EntityPlayAnimation example, a 3D model was added, an animation state graph was created, animation states were created and a trigger area was created to initiate the animations. In this example, an animation event is created for the end of the animation.

{% stepper %}
{% step %}
### Open animation file that will have the animation event

A. Click on the animation file to open up the properties.

<figure><img src="../../../.gitbook/assets/image (507).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Create animation event

A. Create an event, add value **8** to the **time** field and **end** to the **name** field. The value 8 is used because the dancing animation has a duration of 8.83 seconds.

<figure><img src="../../../.gitbook/assets/image (508).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the EntitySubscribeAnimationEnd trigger &#x20;

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeAnimationEnd**.

C. Add an **Action** and select **EntityEnableById.**

D. For the **EntityEnableById** action to work, an object needs to be added that will be enabled. In this example, the **AnimationEndText** object is added.

<figure><img src="../../../.gitbook/assets/image (510).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}







## EntitySubscribeAnimationEvent

**Create A Trigger Based On A Specific Time During An Animation**

This guide provides instructions for setting up the **EntitySubscribeAnimationEvent** trigger. In the sample app, when the avatar enters the blue trigger area, the character’s dancing animation begins to play. The dancing animation has an animation event added and the animation event is triggered during the animation.



| <img src="../../../.gitbook/assets/image (44).png" alt="" data-size="original"> | <img src="../../../.gitbook/assets/image (45).png" alt="" data-size="original">                                                                  |
| ------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| The character’s dancing animation has started.                                  | During the character’s dancing animation, the animation event is triggered and text is displayed to show that the animation event was triggered. |

In the EntityPlayAnimation example, a 3D model was added, an animation state graph was created, animation states were created and a trigger area was created to initiate the animations. In this example, an animation event is created for during the animation.



{% stepper %}
{% step %}
### Open animation file that will have the animation event

A. Click on the animation file to open up the properties.

<figure><img src="../../../.gitbook/assets/image (46).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Create animation event

A. Create an event, add value **4** to the **time** field and create a unique name for the event. In this example, the event name **animate** was added to the **name** field.

<figure><img src="../../../.gitbook/assets/image (47).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the EntitySubscribeAnimationEvent trigger

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeAnimationEvent**.

C. Add the event name to the **event name to subscribe**.

D. Add an **Action** and select **EntityEnableById.**

E. For the **EntityEnableById** action to work, an object needs to be added that will be enabled. In this example, the **AnimationEventText** object is added.

<figure><img src="../../../.gitbook/assets/image (48).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}







## EntityPlaySound

**Create An Action That Plays A Sound**

This guide provides instructions for setting up the EntityPlaySound action. In the sample app, once the avatar enters the trigger area, a sound plays.

| <img src="../../../.gitbook/assets/image (36) (1).png" alt="" data-size="original"> | <img src="../../../.gitbook/assets/image (37) (1).png" alt="" data-size="original"> |
| ----------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| The avatar is outside of the blue trigger area and the sound does not play.         | The avatar enters the blue trigger area and the sound plays.                        |

In this example, a trigger is created and when triggered, an action plays a sound. Any object can be used as a trigger, as long as the object has a collision component. This example uses a 3D box as the trigger area.

{% stepper %}
{% step %}
### Create a trigger area

A. Create a new **3D Box** entity.

B. The **Collision** component is not required for **EntityPlaySound** action to work. The **Collision** component is required for the **EntitySubscribeTriggerEnter** trigger that will be used in this example.

C. Adding a material is optional. A transparent material has been added so that the trigger area is visible in play mode.

D. Add a **Sound** component.

E. Create a unique name for the sound file and add it to the **name** field. In this example, **Slot2** is added.

F. Add the audio file to the **Asset** field.

G. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (38) (1).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the EntityPlaySound action

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeTriggerEnter**.

C. Add **local-player** to the **tags to filter** field.

D. Add an **Action** and select **EntityPlaySound.**

E. In the **sound name to play** field, add the same name that was created on the **Sound** component.

F. Add the entity with the **Sound** component to the **pick up specify execution entity**.

<figure><img src="../../../.gitbook/assets/image (39) (1).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}





## EntityStopSound

**Create An Action That Stops Playing A Sound**

This guide provides instructions for setting up the **EntityStopSound** action. In the sample app, once the avatar leaves the trigger area, the sound stops playing.

| <img src="../../../.gitbook/assets/image (40).png" alt="" data-size="original"> | <img src="../../../.gitbook/assets/image (41).png" alt="" data-size="original"> |
| ------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| The avatar enters the blue trigger area and the sound plays.                    | The avatar is outside of the blue trigger area and the sound stops playing.     |

In this example, a trigger is created and when triggered, an action plays a sound. Any object can be used as a trigger, as long as the object has a collision component. This example uses a 3D box as the trigger area.

{% stepper %}
{% step %}
### Create a trigger area

A. Create a new **3D Box** entity.

B. The **Collision** component is not required for **EntityStopSound** action to work. The **Collision** component is required for the **EntitySubscribeTriggerLeave** trigger that will be used in this example.

C. Adding a material is optional. A transparent material has been added so that the trigger area is visible in play mode.

D. Add a **Sound** component.

E. Create a unique name for the sound file and add it to the **name** field. In this example, **Slot2** is added.

F. Add the audio file to the **Asset** field.

G. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (42).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the EntityStopSound action

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeTriggerLeave**.

C. Add **local-player** to the **tags to filter** field.

D. Add an **Action** and select **EntityStopSound.**

E. In the **sound name to play** field, add the same name that was created on the **Sound** component.

F. Add the entity with the **Sound** component to the **pick up specify execution entity**.

<figure><img src="../../../.gitbook/assets/image (43).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}
