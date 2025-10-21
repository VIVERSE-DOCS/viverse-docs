---
description: >-
  This document provides an introduction to MJS (.mjs) also known as Modular
  JavaScript
---

# Introduction to MJS

***

Modular JavaScript is an evolved form of JavaScript in which related functionality is kept in a single file or module and that functionality is exposed when required using import and export functionality.&#x20;

## CommonJS (NodeJS) example

Here's a comparison between MJS and CommonJS. The latter requires node.js, and relies on `require()`  statements to import other scripts/modules:

```javascript
// To include the File System module, use the require() method
const fs = require('fs');

// Using the File System module to read files
fs.readFile();

// Exporting the greet function so it can be used in other modules, use module.exports
module.exports = function greet(name) {
    return 'Hello, ${name}!';
};
```

MJS, on the other hand, can use import statements like other languages.

```javascript
// To include the File System module, you no longer use require() method
// Use import instead.
import fs from 'fs';

// Using the File System module to read files
fs.readFile();

// Exporting the greet function so it can be used in other modules, you no longer
// use `module.exports`. Use `export` instead.
export const greet = (name) => {
    return 'Hello, ${name}!';
};
```

## MJS PlayCanvas Script Structure

Further, MJS is now supported by PlayCanvas and the VIVERSE Create SDK, to make script imports easier and mroe modern. Here's an example of a script called `GameManager.mjs` (critically, the file extension is `.mjs`, not just `.js` as usual) created for PlayCanvas, extending its built-in `Script` class:

```javascript
import { Script } from 'playcanvas';

export class GameManager extends Script {
    /**
     * Called when the script is about to run for the first time.
     */
    initialize() {
    }

    /**
     * Called for enabled (running state) scripts on each tick.
     * 
     * @param {number} dt - The delta time in seconds since the last frame.
     */
    update(dt) {
    }
}
```

## Accessing a function in a MJS(.mjs) file from regular JavaScript (.js) code example

**DoorRotator.mjs** can can be added to a PlayCanvas entity. This allows functions to be executed on that entity such as a door that needs to be opened. After adding additional code inside the rotateDoor function, this function can be called from another script to open the door.

```javascript
import { Script } from 'playcanvas';

export class DoorRotator extends Script {
    /**
     * @attribute
     * @type {number}
     * @title First Number
     */
    firstNumber = 10;
    
    initialize() {
        // Add the DoorRotator script to this.app
        this.app.doorRotator = this;
    }

    rotateDoor() {
        console.log('Rotate Door!');
    }
}
```

**TriggerArea.js** - Script can be added to a trigger area entity. When another entity or the avatar enters the trigger area, the rotateDoor function is called and the door will open.

```javascript
var TriggerArea = pc.createScript('triggerArea');

TriggerArea.prototype.initialize = function() {
    // Setup listening for the triggerenter event
    this.entity.collision.on('triggerenter', this.onTriggerEnter, this);
};

// Handle the onTriggerEnter event
TriggerArea.prototype.onTriggerEnter = function(entity) {
    // Calling function in DoorRotator.mjs
    this.app.doorRotator.rotateDoor();

    // Accessing property in DoorRotator.mjs
    console.log("FirstNumber: " + this.app.doorRotator.firstNumber);
}
```



## Debugging MJS files

The process for debugging MJS files is simple. You can add the `debugger;` statement to any of the methods.

```javascript
import { Script } from 'playcanvas';

export class Debug extends Script {
    /**
     * Called when the script is about to run for the first time.
     */
    initialize() {
        debugger;
        console.log("Debug initialize!");

        this.testFunction();
    }

    testFunction() {
        debugger;
        console.log('Test Function!');
    }
}
```

With the project running in Chrome, pressing **F12** will bring up the Chrome DevTools window. **Refresh** the page with the DevTools window open and the project will stop at the debugger statement.

<figure><img src="../../.gitbook/assets/image (632).png" alt=""><figcaption></figcaption></figure>

When testing your project in PlayCanvas Launch mode, the Chrome Dev Tools can also be opened by right-clicking on the screen and selecting Inspect.

{% stepper %}
{% step %}
### Right-click on the viewing area of the project



<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="368"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Select Inspect

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}

Open up Chrome DevTools through browser menu

{% stepper %}
{% step %}
### Click the Customize and control Google Chrome Button in the browser

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Click More Tools

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="356"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Click Developer Tools

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}

Keyboard shortcut for DevTools window

{% tabs %}
{% tab title="Windows" %}
<div align="left"><figure><img src="../../.gitbook/assets/WindowsChromeDevTools (1).jpg" alt=""><figcaption></figcaption></figure></div>
{% endtab %}

{% tab title="Mac" %}
<div align="left"><figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="443"><figcaption></figcaption></figure></div>
{% endtab %}
{% endtabs %}

Breakpoints can also be set manually by clicking on the line code number in the margin. The line code number will be highlighted in blue when a breakpoint is set.

<figure><img src="../../.gitbook/assets/image (634).png" alt=""><figcaption></figcaption></figure>

## MJS file with custom properties and methods example

**GameManager.mjs** - GameManager script can be used to control the different game states of the project. This example script has multiple properties of different types added (including arrays), some with default values. In addition to the properties, there are multiple functions added that show how these properties can be utilized and their values are printed to the console.

````javascript
import { Script } from 'playcanvas';

export class GameManager extends Script {
    /**
     * @attribute
     * @type {number}
     * @title First Number
     */
    firstNumber

    /**
     * @attribute
     * @type {number}
     * @title Second Number
     */
    secondNumber = 5;

    /**
     * @attribute
     * @type {string}
     * @title First String
     */
    firstString

    /**
     * @attribute
     * @type {string}
     * @title Second String
     */
    secondString = 'Default Text';

    /**
     * @attribute
     * @type {boolean}
     * @title First Boolean
     */
    firstBoolean

    /**
     * @attribute
     * @type {boolean}
     * @title Second Boolean
     */
    secondBoolean = false;

    /**
    * @attribute
    * @type { pc.Entity }
    * @title Entity
    */
    entity

    /**
     * @attribute
     * @type {number[]}
     * @title Number Array
     */
    numberArray

    /**
     * @attribute
     * @type {string[]}
     * @title String Array
     */
    stringArray

    /**
     * @attribute
     * @type {boolean[]}
     * @title Boolean Array
     */
    booleanArray

    /**
    * @attribute
    * @type { pc.Entity[] }
    * @title Entities
    */
    entityArray

    /**
     * @attribute
     * @type { CustomObject[] }
     * @title Custom Objects Array
     */
    customObjectArray

    /**
     * Called when the script is about to run for the first time.
     */
    initialize() {
        this.changeFirstNumber();
        this.printValue("FirstNumber: " + this.firstNumber);
        this.printValue("SecondNumber: " + this.secondNumber);

        this.modifyFirstString();
        this.printValue("FirstString: " + this.firstString);
        this.printValue("SecondString: " + this.secondString);

        this.modifyFirstBoolean();
        this.printValue("FirstBoolean: " + this.firstBoolean);
        this.printValue("SecondBoolean: " + this.secondBoolean);

        this.printValue("Entity Name: " + this.entity.name);

        this.printValue("Number Array: ");
        this.printArray(this.numberArray);

        this.printValue("String Array: ");
        this.printArray(this.stringArray);

        this.printValue("Boolean Array: ");
        this.printArray(this.booleanArray);

        this.printValue("Entity Array: ");
        this.printEntityArray(this.entityArray);

        this.printValue("CustomObjectArray: ");
        this.printCustomObjectArray(this.customObjectArray);
    }

    /**
     * Called for enabled (running state) scripts on each tick.
     * 
     * @param {number} dt - The delta time in seconds since the last frame.
     */
    update(dt) {
    }

    changeFirstNumber() {
        this.firstNumber = 3;
    }

    modifyFirstString() {
        this.firstString = "modified string";
    }

    modifyFirstBoolean() {
        this.firstBoolean = true;
    }

    printValue(value) {
        console.log(value);
    }

    printArray(array) {
        for (var x = 0; x < array.length; x++) {
            console.log(array[x]);
        }
    }

    printEntityArray(array) {
        for (var x = 0; x < array.length; x++) {
            console.log(array[x].name);
        }
    }

    printCustomObjectArray(array) {
        for (var x = 0; x < array.length; x++) {
            console.log(array[x].valueOf());
        }
    }
}

/** @interface */
class CustomObject {
    /**
     * @attribute
     * @type { number }
     * @title Number
     */
    number

    /**
     * @attribute
     * @type { pc.Entity[] }
     * @title Entities Array
     */
    entities
}
```
````



## Key Examples

<details>

<summary>localPlayerManager.mjs - Control <a href="https://viveportsoftware.github.io/pc-lib/interfaces/ILocalPlayer.html">Local Player Properties</a></summary>

```javascript
import { Script } from 'playcanvas';
import * as pc from 'playcanvas';
import { PlayerService } from '../@viverse/create-sdk.mjs'


export class LocalPlayerManager extends Script 
{
    initialize() {
        this.playerService  = new PlayerService();
        
        //attach playerService for global access, reference this.app.playerServiceManager in other files
        this.app.playerServiceManager = this;
        
        //FOR ALL CUSTOMIZABLE PROPS AND METHODS, see: https://viveportsoftware.github.io/pc-lib/interfaces/ILocalPlayer.html
        
        //enable flight
        this.playerService.localPlayer.canFly = true;
        
        //enable movement
        this.playerService.localPlayer.canMove = true;
        
        //hide avatar
        this.playerService.localPlayer._entity.visibility = false;
    }

    update(dt)
    {
    }
}
```

</details>

<details>

<summary>cameraServiceManager.mjs - Control <a href="https://viveportsoftware.github.io/pc-lib/interfaces/ICameraService.html">Camera Service Properties</a></summary>

```javascript
import { Script } from 'playcanvas';
import * as pc from "playcanvas"
import { CameraService } from './@viverse/create-sdk.mjs'

/**
 * The {@link https://api.playcanvas.com/classes/Engine.Script.html | Script} class is
 * the base class for all PlayCanvas scripts. Learn more about writing scripts in the
 * {@link https://developer.playcanvas.com/user-manual/scripting/ | scripting guide}.
 */
export class CameraServiceManager extends Script {
    /**
     * Called when the script is about to run for the first time.
     */
    initialize() {
        this.cameraService  = new CameraService();
        
        //FOR ALL CUSTOMIZABLE PROPS AND METHODS, see https://viveportsoftware.github.io/pc-lib/interfaces/ICameraService.html
        
        //switch to 1st person pov
        this.cameraService.switchPov(0);
        
        //prevent pov switching
        this.cameraService.canSwitchPov = false;
    }

    /**
     * Called for enabled (running state) scripts on each tick.
     * 
     * @param {number} dt - The delta time in seconds since the last frame.
     */
    update(dt) {
    }
}
```

</details>
