---
description: >-
  Learn how use Actions in your VIVERSE Worlds, and how to link them to Triggers
  and / or Custom Scripts
---

# Actions

***

## About

Actions is another fundamental part of VIVERSE Framework, allowing creators to devise a wide range of possible "verbs" that their experience can do, without writing a single line of code. The typical examples are:

* Enable / Disable / Toggle Entity or a group of Entities
* Teleport Player to a new location
* Fade In or Fade Out some Entity
* Play Animation / Particle Effect or Sound
* Initialize Quest or advance particular Quest Task
* And so on, and so forth

## Usage

Actions are typically paired with [Triggers](triggers.md), where one Trigger can execute multiple Actions in parallel, or one Action can be executed by any Trigger matching its name. It's also possible to execute Actions from your [Custom Scripts](custom-scripts.md), opens up even more possibilities for interactivity!

Here is a simple example of an Action being executed by a Trigger:

{% columns %}
{% column width="66.66666666666666%" %}
{% stepper %}
{% step %}
### Setup Trigger

* Create a new empty Entity in your Scene
* Add **3D > Render Component** of type `Box`. Your Entity should be visible in your Scene now
* Add **Physics > Collision Component** of type `Box` as well. Adjust its params if necessary
* Click **Add Viverse Component** button and select **Rule > Trigger**. The `viverseTrigger` script will be added to your Entity
* In Trigger Component, add a new entry to the Trigger List, by entering 1
* Leave Trigger type at `OnSelect` (default), and give it a unique `Name`, for example `box.clicked`
{% endstep %}

{% step %}
### Setup Action

* With your Entity selected, click **Add Viverse Component** button once again, and select **Rule > Action**. The `viverseAction` script will be added to your Entity
* In Action Component, add a new entry to the Action List, by entering 1
* Populate `Trigger / Condition` field with your Trigger Name, which is `box.clicked` in our case
* Leave all other params at their defaults - `ToggleEntities`, `Toggle`, `Self`&#x20;
{% endstep %}

{% step %}
### Test and verify

* Now we can test our Trigger / Action pair!
* Launch your Scene in a new tab, walk towards white box Entity and click it
* If you set up everything correctly — the Entity should disappear on your click
{% endstep %}
{% endstepper %}
{% endcolumn %}

{% column width="33.33333333333334%" %}
<figure><img src="../../.gitbook/assets/tr1a.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/tr2.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/tr5.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/tr3.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/tr4.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/tr6.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

{% hint style="info" %}
It's not really necessary to have both **Trigger** and **Action** Components on the same Entity! For the purpose of your experience you can have as many Entities as you like, each with its own Trigger or Action, or a combination of those.

The only important rule is that Trigger's `Name` param should match an Action's `Trigger / Condition` one if you want them to be linked together
{% endhint %}

## Reference

***

{% columns %}
{% column width="25%" %}
`Toggle`\
`Entities`
{% endcolumn %}

{% column width="50%" %}
Enables or disables a given Entity according to desired `State` parameter. Supported states:

* `Enable` / `Disable` / `Toggle`&#x20;

What Entity is enabled or disabled can be controlled by **Entity Filter** selector:

* `Self` - current Entity (default)
* `Tag` - all Entities with specific Tag
* `TargetEntity` - particular Entity in the Scene
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/ac1.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/ac1b.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`Toggle`\
`Component`
{% endcolumn %}

{% column width="50%" %}
Enables or disables particular Component on a given Entity according to desired `State` parameter. Supported states:

* `Enable` / `Disable` / `Toggle`&#x20;

Supported Components to destroy:

* `Collision` / `Rigidbody`

As with `ToggleEntities` type, **Entity Filter** selector controls what Entity is affected by this Action
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/ac2.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`Destroy`\
`Entities`
{% endcolumn %}

{% column width="50%" %}
Destroys a given Entity based on **Entity Filter** selector. See `ToggleEntities` above for available filter options
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/ac3.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`Fade`\
`Transition`
{% endcolumn %}

{% column width="50%" %}
Fades In or Out a given Entity over `Duration` period. Supported Fade Types:

* `FadeIn` / `FadeOut`&#x20;

The Entity should have **Mesh** or **Render Component** attached, with transparent Materials.

As with `ToggleEntities` type, **Entity Filter** selector controls what Entity is affected by this Action
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/ac4.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/ac4b.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`Push`\
`Notification`
{% endcolumn %}

{% column width="50%" %}
Fires a Notification Event with a given `Event Name`. This event can be picked up by another Trigger Entity of type `OnNotificationEvent`. You can use it to send / receive custom Notification Events between different Entities in your Scene
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/ac5.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`Teleport`\
`Player`
{% endcolumn %}

{% column width="50%" %}
Teleports the Player to a given 3D point in your Scene
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/ac6.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`Push`\
`Entity`
{% endcolumn %}

{% column width="50%" %}
Applies a 3D impulse to a given Entity, provided this Entity has **Rigidbody Component** attached. As with `ToggleEntities` type, **Entity Filter** selector controls what Entity is affected by this Action
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/ac7.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`Sound`\
`Control`
{% endcolumn %}

{% column width="50%" %}
Plays or stops a Sound on a given Entity, provided this Entity has **Sound Component** attached. `Audio Name` refers to a **Sound Slot** name. Supported actions:

* `Play` / `Stop`

As with `ToggleEntities` type, **Entity Filter** selector controls what Entity is affected by this Action
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/ac8.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`Animation`\
`Control`
{% endcolumn %}

{% column width="50%" %}
Plays an Animation on a given Entity with `Duration` blend time, provided this Entity has **Animation Component** attached. `Animation Name` refers to an **Animation Clip**. As with `ToggleEntities` type, **Entity Filter** selector controls what Entity is affected by this Action
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/ac9.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`Particle`\
`System` \
`Control`
{% endcolumn %}

{% column width="50%" %}
Plays a Particle Effect on a given Entity, provided this Entity has **Particle System Component** attached. As with `ToggleEntities` type, **Entity Filter** selector controls what Entity is affected by this Action
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/ac10.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`Quest`
{% endcolumn %}

{% column width="50%" %}
Updates state of Quest with a given `Quest Name`, based on `Command` provided. Supported commands:

* `Start Quest` / `Reset Quest`
* `AddTaskProgress` / `CompleteTask`&#x20;
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/ac11.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`Set` \
`Spawn`\
`Point`
{% endcolumn %}

{% column width="50%" %}
Sets the next Spawn Point for the Player to a 3D point from provided list. If the list contains multiple options - the point will be selected randomly out of them
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/ac12.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`Seat`&#x20;
{% endcolumn %}

{% column width="50%" %}
Executes Player sitting mechanic for a given Seat Entity, according to provided `Command`. The Entity should have **Seat Component** attached. Supported commands:

* `Sit` / `Leave`
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/ac14.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`Open` \
`Link`
{% endcolumn %}

{% column width="50%" %}
Opens URL with a given `Link`
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/ac15.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***
