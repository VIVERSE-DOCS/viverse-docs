---
description: >-
  This document provides an introduction to MJS (.mjs) also known as Modular
  JavaScript
---

# Introduction to MJS

Modular JavaScript is an evolved form of JavaScript in which related functionality is kept in a single file or module and that functionality is exposed when required using import and export functionality. Here's a comparison breakdown between CommonJS (NodeJS) and MJS.

## CommonJS (NodeJS) example

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



## MJS example

```javascript
// To include the File System module, you no longer use require() method
// Use import instead.
import fs from 'fs';

// Using the File System module to read files
fs.readFile();

// Exporting the greet function so it can be used in other modules, you no longer
// use module.exports.
// Use export instead.
export const greet = (name) => {
    return 'Hello, ${name}!';
};

```



## MJS Structure

Here's an example of how a script called GameManager.mjs would look if created in PlayCanvas.

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

#### DoorRotator.mjs

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

#### TriggerArea.js

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

Keyboard shortcut for DevTools window

<figure><img src="../../.gitbook/assets/image (633).png" alt=""><figcaption></figcaption></figure>

Breakpoints can also be set manually by clicking on the line code number in the margin. The line code number will be highlighted in blue when a breakpoint is set.

<figure><img src="../../.gitbook/assets/image (634).png" alt=""><figcaption></figcaption></figure>

## MJS with custom properties and methods example

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
