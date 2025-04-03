---
description: >-
  This document provides a guide for setting up no-code events in custom
  scripts.
---

# Connecting No-Code Events to Custom Scripts

***

The VIVERSE no-code events can be accessed through custom scripts. The events can be found in the root directory of your PlayCanvas project in the **@viverse/create-extensions-sdk.mjs** file. Here's a look at the file.

```javascript
export const TriggerTypes = {
  Base: 'trigger:0',
  Echo: 'trigger:1',
  PCAppEventSubscribe: 'trigger:100001',
  NotificationCenterSubscribe: 'trigger:200001',
  NotificationCenterSubscribeEntityPicking: 'trigger:200002',
  TheatreJSSubscribe: 'trigger:210001',
  TheatreJSSubscribeSheetEnd: 'trigger:210002',
  EntitySubscribeAnimationEvent: 'trigger:300001',
  EntitySubscribeAnimationStart: 'trigger:300002',
  EntitySubscribeAnimationEnd: 'trigger:300003',
  EntitySubscribeCollisionStart: 'trigger:300004',
  EntitySubscribeCollisionEnd: 'trigger:300005',
  EntitySubscribeTriggerEnter: 'trigger:300006',
  EntitySubscribeTriggerLeave: 'trigger:300007',
  SitInSeat: 'trigger:300008',
  SharePhoto: 'trigger:400001',
  EnterAnyWorld: 'trigger:400002',
  EnterMyWorld: 'trigger:400003'
}

export const ActionTypes = {
  Base: 'action:0',
  Echo: 'action:1',
  PCAppEventPublish: 'action:100001',
  NotificationCenterPublish: 'action:200001',
  TheatreJSPublish: 'action:210001',
  TheatreJSPlaySheet: 'action:210002',
  EntityRigidbodyAddForceInPhysics: 'action:300001',
  EntityPlayAnimation: 'action:300002',
  EntityEnable: 'action:300003',
  EntityDisable: 'action:300004',
  EntityToggleEnabled: 'action:300005',
  EntityFadeIn: 'action:300006',
  EntityFadeOut: 'action:300007',
  EntityPlaySound: 'action:300008',
  EntityEnableCollision: 'action:300009',
  EntityDisableCollision: 'action:300010',
  EntityToggleCollision: 'action:300011',
  InworldNpcWaitingSpeak: 'action:300012',
  InworldNpcStopWaitingSpeak: 'action:300013',
  EntityEnableByTag: 'action:300014',
  EntityDisableByTag: 'action:300015',
  EntityCheckPoint: 'action:300016',
  EntityEnableById: 'action:300017',
  EntityDisableById: 'action:300018',
  EntityStopSound: 'action:3000019',
  EntityAssetUnload: 'action:3000020',
  EntityAssetReload: 'action:3000021',
  EntityDestroy: 'action:3000022',
  ParticleSystemPlay: 'action:3000023',
  Quest: 'action:400001',
  TeleportAvatar: 'action:400002',
  AssignUserAsset: 'action:400003',
  Vote: 'action:400004',
  ShowToastMessage: 'action:400005',
  TaskComplete: 'action:400006'
}
```



## Trigger usage

**Calling custom code from script when the player clicks on an object**

Let's say we want to give our players the ability to click on an object and when that object is clicked, we want to call a method from a custom script. This is a good use-case for the **NotificationCenterSubscribeEntityPicking** trigger. In this example, when the user clicks on the specific object, a door opens.

{% stepper %}
{% step %}
### Add the NotificationCenterSubscribeEntityPicking trigger

A. Select the object that the player will click on and add the **NotificationCenterSubscribeEntityPicking** trigger using the PlayCanvas extension. No need to add an action.

<div align="left"><figure><img src="../../.gitbook/assets/image (618).png" alt=""><figcaption></figcaption></figure></div>
{% endstep %}

{% step %}
### Create the ClickableObject.mjs script

A. Create the following script and name it **ClickableObject.mjs**.

```javascript
import { Script } from 'playcanvas';
import { TriggerTypes } from '../@viverse/create-extensions-sdk.mjs';

export class ClickableObject extends Script {
    /**
     * Called when the script is about to run for the first time.
     */
    initialize() {
        const event = TriggerTypes.NotificationCenterSubscribeEntityPicking;
        this.entity.on(event, () => {
            this.objectClicked();
            this.app.fire('DoorRotator:rotateDoor');
        });
    }

    /**
     * Called for enabled (running state) scripts on each tick.
     * 
     * @param {number} dt - The delta time in seconds since the last frame.
     */
    update(dt) {
    }

    objectClicked() {
        console.log('Objected Clicked!');
    }
}
```

B. Add **ClickableObject.mjs** script to the object that the player will click on.
{% endstep %}

{% step %}
### Create the DoorRotator.mjs script

A. Create the following script and name it **DoorRotator.mjs**.

```javascript
import { Script } from 'playcanvas';

export class DoorRotator extends Script {
    /**
     * Called when the script is about to run for the first time.
     */
    initialize() {
        // listen for the DoorRotate:rotateDoor event
        this.app.on('DoorRotator:rotateDoor', this.rotateDoor);
    }

    /**
     * Called for enabled (running state) scripts on each tick.
     * 
     * @param {number} dt - The delta time in seconds since the last frame.
     */
    update(dt) {
    }

    rotateDoor() {
        console.log('Rotate Door!');
    }
}
```

B. Add the **DoorRotator.mjs** script to an object or entity.
{% endstep %}

{% step %}
### Confirm the event is being fired

A. Test the functionality by publishing to VIVERSE and clicking on the object that has the **ClickableObject.mjs** script added to it.&#x20;

B. Confirm the event was fired by checking in the browser debug console.

<div align="left"><figure><img src="../../.gitbook/assets/image (619).png" alt=""><figcaption></figcaption></figure></div>
{% endstep %}
{% endstepper %}



## Tigger usage with more flexibility

**Calling custom code from script when the player clicks on an object and making the script more flexible**

In the previous example, we created a custom script called **ClickableObject.mjs.** The script added functionality that rotates a door whenever a specific object is clicked.  Let's say there was another scenario where we wanted to have a 2nd object that the user could click on, but we wanted to fire a different event other than the rotateDoor method. We could easily copy our **ClickableObject.mjs** script, give it a different name and change the rotateDoor call inside the script. The downside here is that we now have 2 scripts that we have to maintain. As alternative, we can remove the rotateDoor event call, add an attribute to our script for the event name and then add the different event names through the PlayCanvas editor.

{% stepper %}
{% step %}
### Update the ClickableObject.mjs script

A. Modify the code in **ClickableObject.mjs** script to the following.

```javascript
import { Script } from 'playcanvas';
import { TriggerTypes } from '../@viverse/create-extensions-sdk.mjs';

export class ClickableObject extends Script {
    /**
     * @attribute
     * eventToFire
     * @type { string }
     * @title Event To Fire
     */
    eventToFire

    /**
     * Called when the script is about to run for the first time.
     */
    initialize() {
        const event = TriggerTypes.NotificationCenterSubscribeEntityPicking;
        this.entity.on(event, () => {
            this.objectClicked();
            this.app.fire(this.eventToFire);
        });
    }

    /**
     * Called for enabled (running state) scripts on each tick.
     * 
     * @param {number} dt - The delta time in seconds since the last frame.
     */
    update(dt) {
    }

    objectClicked() {
        console.log('Objected Clicked!');
    }
}
```
{% endstep %}

{% step %}
### Add the event name in to the script inside the PlayCanvas editor

A. Add the **DoorRotator:rotateDoor** text to the **Event To Fire** attribute text field.

<div align="left"><figure><img src="../../.gitbook/assets/image (622).png" alt=""><figcaption></figcaption></figure></div>

B. If we added **ClickableObject.mjs** to a 2nd object and wanted to fire a different method, it could look like the following screenshot.

<div align="left"><figure><img src="../../.gitbook/assets/image (621).png" alt=""><figcaption></figcaption></figure></div>
{% endstep %}

{% step %}
### Confirm the event is being fired

A. Test the functionality by publishing to VIVERSE and clicking on the object that has the **ClickableObject.mjs** script added to it.&#x20;

B. Confirm the event was fired by checking in the browser debug console. It should yield the same results as in the previous example.

<div align="left"><figure><img src="../../.gitbook/assets/image (623).png" alt=""><figcaption></figcaption></figure></div>
{% endstep %}
{% endstepper %}

## Additional trigger usage

**Calling custom code from script when a player enters a trigger area**

Another common scenario that creators may face is firing an event when a player enters a trigger area. This is a good use-case for the **EntitySubscribeTriggerEnter** trigger. In this example, when the user enters a trigger area, a door opens.

{% stepper %}
{% step %}
### Add the EntitySubscribeTriggerEnter trigger

A. Select the trigger area that the player will walk through and add the **EntitySubscribeTriggerEnter** trigger using the PlayCanvas extension. No need to add an action.

<div align="left"><figure><img src="../../.gitbook/assets/image (624).png" alt=""><figcaption></figcaption></figure></div>
{% endstep %}

{% step %}
### Create the TriggerArea.mjs script

A. Create the following script and name it **TriggerArea.mjs**.

```javascript
import { Script } from 'playcanvas';
import { TriggerTypes } from '../@viverse/create-extensions-sdk.mjs';

export class TriggerArea extends Script {
    /**
     * Called when the script is about to run for the first time.
     */
    initialize() {
        const event = TriggerTypes.EntitySubscribeTriggerEnter;
        this.entity.on(event, () => {
            this.triggerEntered();
            this.app.fire('DoorRotator:rotateDoor');
        });
    }

    /**
     * Called for enabled (running state) scripts on each tick.
     * 
     * @param {number} dt - The delta time in seconds since the last frame.
     */
    update(dt) {
    }

    triggerEntered() {
        console.log('Trigger Entered!');
    }
}
```

B. Add **TriggerArea.mjs** script to trigger area that the player will walk through.
{% endstep %}

{% step %}
### Confirm the event is being fired

A. Test the functionality by publishing to VIVERSE and walking through the trigger area that has the **TriggerArea.mjs** script added to it.&#x20;

B. Confirm the event was fired by checking in the browser debug console. It should yield the following results.

<div align="left"><figure><img src="../../.gitbook/assets/image (626).png" alt=""><figcaption></figcaption></figure></div>
{% endstep %}
{% endstepper %}

## Action usage

**Calling custom code from script when an action executed**

Let's say we want to give our players the ability to click on an object and when that object is clicked, we want to call a method from a custom script. This is a good use-case for the **NotificationCenterSubscribeEntityPicking** trigger. In this example, when the user clicks on the specific object, a door opens.

{% stepper %}
{% step %}
### Add the EntitySubscribeTriggerEnter trigger and the EntityDisable action

A .Select the trigger area that the player will walk through and add the **EntitySubscribeTriggerEnter** trigger along with the EntityDisable action using the PlayCanvas extension.

<div align="left"><figure><img src="../../.gitbook/assets/image (627).png" alt=""><figcaption></figcaption></figure></div>
{% endstep %}

{% step %}
### Create the DisabledObject.mjs script

A. Create the following script and name it **DisableObject.mjs**.

```javascript
import { Script } from 'playcanvas';
import { ActionTypes } from '../@viverse/create-extensions-sdk.mjs';

export class DisabledObject extends Script {
    /**
     * Called when the script is about to run for the first time.
     */
    initialize() {
        const event = ActionTypes.EntityDisable;
        this.entity.on(event, () => {
            this.objectDisabled();
            this.app.fire('DoorRotator:rotateDoor');
        });
    }

    /**
     * Called for enabled (running state) scripts on each tick.
     * 
     * @param {number} dt - The delta time in seconds since the last frame.
     */
    update(dt) {
    }

    objectDisabled() {
        console.log('Object Disabled!');
    }
}
```

B. Add **DisabledObject.mjs** script to trigger area that the player will walk through. In this example the **DisabledObject.mjs** script was added to the trigger area in the previous example. Be sure to disable or remove the **TriggerArea.mjs** script from the previous example.

<div align="left"><figure><img src="../../.gitbook/assets/image (629).png" alt=""><figcaption></figcaption></figure></div>
{% endstep %}

{% step %}
### Confirm the event is being fired

A. Test the functionality by publishing to VIVERSE and walking through the trigger area that has the **DisabledObject.mjs** script added to it.&#x20;

B. Confirm the event was fired by checking in the browser debug console. It should yield the following results.

<div align="left"><figure><img src="../../.gitbook/assets/image (628).png" alt=""><figcaption></figcaption></figure></div>
{% endstep %}
{% endstepper %}
