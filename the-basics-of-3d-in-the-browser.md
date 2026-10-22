---
description: Learn the fundamentals of browser rendering.
icon: window-flip
---

# The Basics of 3D in the Browser

***

## Introduction

3D rendering engines are an abstraction around rendering APIs that simplify the process of drawing meshes on the screen and simulating the lighting of the meshes. In the web, rendering engines are composed of:

* A [rendering pipeline](https://www.khronos.org/opengl/wiki/Rendering_Pipeline_Overview) built on top of a browser rendering API, which updates every frame.
* A developer-facing API for initializing and updating meshes, materials, cameras, and lights, written in a browser-supported programming language.
* Abstractions for connecting the output of the rendering pipeline to the browser window.
* Abstractions for updating the scene in response to user inputs received from browser APIs.

<figure><img src=".gitbook/assets/Render Loop.png" alt="Rendering Image Design: User Input -> State Updates -> Rendering -> Output" width="563"><figcaption></figcaption></figure>

## :art: Core Browser Technologies: WebGL and HTMLCanvas

{% hint style="info" %}
Modern web browsers, such as Safari, Chrome, Firefox, and Edge, implement a set of standardized Graphics APIs and HTML components that collectively support rendering 3D applications in a web page.&#x20;
{% endhint %}

* The primary browser graphics API is [**WebGL**](https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API), which is based on the native OpenGL graphics API. Over the past two years, engines browsers have also begun to support [**WebGPU**](https://developer.mozilla.org/en-US/docs/Web/API/WebGPU_API), a more modern graphics API that integrates tightly with powerful native APIs like [Vulkan](https://www.vulkan.org/), [Metal](https://developer.apple.com/metal/), and [DirectX 12](https://learn.microsoft.com/en-us/windows/win32/directx). The purpose of a graphics API is to provide an interface between the CPU, which constructs an abstract representation of a scene, and the GPU, which renders the abstract scene representation to an image.
* The GPU outputs the rendered image as a list of color values into a special memory allocation known as the [**framebuffer**](https://en.wikipedia.org/wiki/Framebuffer). In order to display the image to the user, the browser provides an HTML component called the [**canvas**](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/canvas). This canvas component occupies a rectangular area of a webpage, and displays the output from the GPU. In practice, the developer constructs a canvas, and then accesses the appropriate graphics API (WebGL or WebGPU) through a reference to the canvas.

## :desktop: Programming Language Support in Web Rendering Engines

{% hint style="info" %}
The only officially supported browser language is [JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript). To execute non-JavaScript code directly in the browser, it must be compiled to a binary format known as [WebAssembly](https://developer.mozilla.org/en-US/docs/WebAssembly), where it can be directly executed by a virtual machine in the browser.
{% endhint %}

There are two main approaches to building rendering engines for the web:

* Compiling an existing engine written in a low-level languages (e.g. C, C++, Rust) to [WebAssembly](https://developer.mozilla.org/en-US/docs/WebAssembly). This approach is taken by engines like [Unity](https://docs.unity3d.com/2020.3/Documentation/Manual/webgl.html), [Godot](https://docs.godotengine.org/en/stable/about/list_of_features.html#platforms), and [Bevy](https://bevy.org/examples/).
* Writing the engine entirely in JavaScript. This approach is used by engines like [PlayCanvas](https://playcanvas.com/), [Three.js](https://threejs.org/), and [Babylon.js](https://www.babylonjs.com/).

Each approach has certain tradeoffs:

<table><thead><tr><th width="142.6875">Tradeoff</th><th>WebAssembly</th><th>JavaScript</th></tr></thead><tbody><tr><td>Browser Compatibility</td><td><a href="https://webassembly.org/docs/web/">Browser APIs are generally not available to the WebAssembly runtime</a>; to interface with the browser, WebAssembly code must call a JavaScript function that then calls the corresponding browser API. Sending commands and data over this reflection layer can be expensive, negating some of the benefits of the theoretically faster low-level WebAssembly code. </td><td>JavaScript can directly call all browser APIs.</td></tr><tr><td>Performance Ceiling</td><td>WebAssembly runs at near-native speeds, enabling a very high performance ceiling.</td><td>While modern JavaScript engines like <a href="https://v8.dev/">V8</a> and <a href="https://docs.webkit.org/Deep%20Dive/JSC/JavaScriptCore.html">JavaScriptCore</a> are capable of running graphics applications, low-level languages like C++ and Rust have an edge in pure performance. In particular, JavaScript is single-threaded, uses automatic memory management, and cannot leverage <a href="https://en.wikipedia.org/wiki/Single_instruction%2C_multiple_data">Single-Instruction Multiple Data</a> parallelization in the browser, putting a theoretical ceiling on performance. Some multi-threading capability is available in the browser via the <a href="the-basics-of-3d-in-the-browser.md#reducing-scripting-overhead-with-multi-threading">web worker API</a>.</td></tr><tr><td>Debugging</td><td>WebAssembly debugging support is lacking across major browsers. Developers will often need to rely on debugging tools provided by the engine, making it hard to fix web-specific bugs.</td><td>Profiling and debugging tools are built into the browser, making it relatively easy to identify the root cause of performance problems and bugs.</td></tr><tr><td>Deployment</td><td>WebAssembly requires an additional compilation step in the deployment process. The entire game engine must be compiled with the build.</td><td>JavaScript applications can be deployed directly. Developers can also reduce bundle sizes by stripping unused code and <a href="https://github.com/mishoo/UglifyJS">minifying</a> the JavaScript code.</td></tr></tbody></table>

The choice of engine is not as clear-cut as performance vs. usability. Given the complex nature of rendering engines, it is very difficult to build comprehensive performance benchmarks proving one engine is faster than another. It is more important to choose an engine based on the type of experience you want to build and your strengths as a developer.

## :construction\_site: Building a Rendering Pipeline with WebGL

Graphics APIs like WebGL and WebGPU are used to construct a [**rendering pipeline**](https://www.khronos.org/opengl/wiki/Rendering_Pipeline_Overview), a program that defines the steps that the underlying native Graphics API ([OpenGL](https://www.opengl.org/), [Vulkan](https://www.vulkan.org/), [Metal](https://developer.apple.com/metal/), [DirectX](https://learn.microsoft.com/en-us/windows/win32/directx)) must take to render an object to the framebuffer.&#x20;

Here is a code sample of a rendering pipeline implemented in WebGL that draws a single triangle to the screen.  Below, we describe how WebGL and the canvas interact with the GPU to draw the triangle onto the screen.

{% embed url="https://codepen.io/Henry-Allen/embed/PwPezyg" %}

{% hint style="info" %}
Toggle the "JS" and "HTML" tabs to view the usage of the WebGL and the canvas APIs, respectively.
{% endhint %}

{% stepper %}
{% step %}
#### **Creating the Canvas**

The output of the rendering pipeline is rendered directly into the browser via the Canvas API:

```html
<canvas id="myCanvas" width="400" height="400">
    The Rendering Canvas (Alt Text)
</canvas>
```
{% endstep %}

{% step %}
#### Setting Up WebGL State In JavaScript

Before anything happens on the GPU, the developer must set up the pipeline in JavaScript. This consists of: setting up **shaders**, small functions that run on the GPU in parallel to process each triangle and pixel we want to render; allocating **vertex buffers**, which contain data for each vertex, a point in 3D space that makes up a mesh; and describing the layout of vertex buffers, as a vertex buffer can contain position data, color data, etc., laid out in any fashion. After this state is set up, the code submits the frame to the GPU, where the rendering pipeline begins.
{% endstep %}

{% step %}
#### Vertex Processing

3D scenes consist of many vertices in 3D space, which make up lines and triangles, which in turn compose into more complex shapes. In the first step of the rendering pipeline, each vertex in an array of vertices are processed by a "vertex shader", a small program that determines the position and color of a particular vertex given some input properties.
{% endstep %}

{% step %}
#### Primitive Assembly and Clipping

Each vertex may be part of one or more primitives (point, line, or triangle) in the scene. At this stage, the pipeline generates an array of primitives from the vertices and clips all primitives that extend outside of the camera view.
{% endstep %}

{% step %}
#### Rasterization

The processed primitives are then _rasterized_, or converted into a sequence of fragments. Whereas primitives represent a shape in 3D space, fragments represent the projection of 3D shapes into 2D space. Imagine taking a picture on a digital camera; the 3D scene you are capturing is recorded by a series of sensors, which record light information from 3D space. A fragment loosely corresponds to one of these sensors; it consists of 2D position data for a point and data interpolated from the vertices that contribute to that point. This fragment data is output to the next stage.
{% endstep %}

{% step %}
#### Fragment processing

Each fragment is processed by a fragment shader, which determines the final color of each value in the framebuffer.
{% endstep %}

{% step %}
#### Canvas Output

Finally, the framebuffer is drawn onto the canvas in the browser window. View a complete example of the rendering pipeline in this [codepen](https://codepen.io/Henry-Allen/pen/PwPezyg). You can look at the exact JavaScript and HTML used to render a triangle to the screen.
{% endstep %}
{% endstepper %}

## :camera\_with\_flash: Engine Abstractions: Meshes, Materials, Cameras, and Lights

Rendering API code is often regarded as verbose and unapproachable, so some abstractions are built on top to make things easier:

* At the highest level of abstraction is the **scene**, a tree-like data structure that defines the hierarchy of elements that are rendered to the screen.&#x20;
* The scene is composed of **meshes**, collections of triangles that from a shape, and have some position, scale, and rotation in the world.&#x20;
* Each mesh has a **material**, which defines how the mesh responds to **lights** in the scene. Materials may render just a solid color, can render a **texture**, or can imitate real-life material properties, like wood or skin.&#x20;
* A **camera** renders the scene from a particular perspective, which is then output to the browser window via the Canvas API.&#x20;

We might construct a scene like this:

```javascript
// Example of initializing a scene and starting the render loop
function runApp() {
  const scene = engine.createScene();

  const camera = scene.createCamera();
  camera.position = { x: 0, y: 4, z: -15 };
  camera.lookAt({ x: 0, y: 0, z: 0 });

  const light = scene.createAreaLight();
  light.color = { r: 1, g: 1, b: 1 };

  const basicMaterial = scene.createStandardMaterial();
  basicMaterial.color = { r: 1, g: 0, b: 0 };

  const box = scene.createBox();
  box.material = basicMaterial;

  const boxInstance1 = box.createInstance();
  boxInstance1.position = { x: 1, y: 1, z: 0 };
  boxInstance1.scale = { x: 2, y: 1, z: 0 };

  const boxInstance2 = box.createInstance();
  boxInstance2.position = { x: -1, y: -1, z: 0 };

  // RequestAnimationFrame is a browser API that fires a callback on every browser fram
  requestAnimationFrame(function() {
    // Re-render the scene every frame
    render(scene);
  })
}
```

In an engine like Babylon.js, we can create a similar scene in a [playground](https://playground.babylonjs.com/#2KET78#1) and produce the following result:

<figure><img src=".gitbook/assets/Screenshot 2025-08-25 at 7.47.59 PM.png" alt="" width="375"><figcaption></figcaption></figure>

Behind the scenes, the engine will compose a rendering pipeline from these instances, lights, and camera in the `render` function. In most engines, the `render` function will look something [like this](https://toji.dev/webgpu-gltf-case-study/#rendering-with-webgl):

```js
// Heavily-abstracted render loop. This runs every frame.
function render(scene) {
  for (let material of scene.materials) {
    // Initialize Shaders using gl.createShader(), gl.compileShader(), etc.
    InitializeShadersAndTextures(material, scene.camera, scene.lights);

    for (let mesh of scene.meshes.filter(
      (mesh) => mesh.material === material
    )) {
      // Create a WebGL buffer for the mesh vertices using gl.createBuffer()
      InitializeMeshBuffers(mesh);

      for (let instance of mesh.instances.values()) {
        // For each instance of the mesh, set the appropriate color and position
        SetInstanceValues(instance);

        // Render the instance to the screen using gl.drawArrays()
        Draw();
      }
    }
  }
}
```

## :woman\_technologist: Updating the Scene in Response to User Inputs in the Browser

Generally, users need to interact with the program in some way. The browser exposes a few APIs to allow controlled access to the mouse, keyboard, touch, and gamepad events:

<table><thead><tr><th width="200.3359375">Input Method</th><th>API</th><th>Platform Support</th></tr></thead><tbody><tr><td><strong>Touch</strong></td><td><ul><li><a href="https://developer.mozilla.org/en-US/docs/Web/API/Touch_events">Touch Events API</a>: Responds to swipe, tap, and multitap interactions</li></ul></td><td><ul><li>Mobile</li><li>Chrome, Edge Desktop</li></ul></td></tr><tr><td><strong>Keyboard/Mouse</strong> </td><td><ul><li><a href="https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent">KeyboardEvent API</a>: Responds to key strokes </li><li><a href="https://developer.mozilla.org/en-US/docs/Web/API/MouseEvent">MouseEvent API</a>: Responds to mouse clicks and mouse movements</li></ul></td><td><ul><li>Mobile</li><li>Desktop</li></ul></td></tr><tr><td><strong>Gamepad</strong> </td><td><ul><li><a href="https://developer.mozilla.org/en-US/docs/Web/API/Gamepad_API/Using_the_Gamepad_API">Gamepad API</a>: Detects when controllers are connected, and determines the controller layout</li></ul></td><td><ul><li>Mobile</li><li>Desktop</li></ul></td></tr><tr><td><strong>XR Headset and Inputs</strong></td><td><ul><li><a href="https://developer.mozilla.org/en-US/docs/Web/API/WebXR_Device_API">WebXR API</a>: Provides abstractions for handling headset movement and hand/gamepad inputs.</li></ul></td><td><ul><li>XR</li></ul></td></tr><tr><td>Pointer</td><td><ul><li><a href="https://developer.mozilla.org/en-US/docs/Web/API/Pointer_events">Pointer Events API</a>: Treats mouse, touch, and gamepad events as generic "pointer" events. This simplifies responding to basic click and hover events.</li></ul></td><td><ul><li>Mobile, Desktop, XR</li></ul></td></tr></tbody></table>

## :books: More Learning Resources

General WebGL and WebGPU tutorials:

1. [Canvas Tutorial](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial)&#x20;
2. [Learning WebGL](https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API/By_example)&#x20;
3. [WebGL Fundamentals](https://webglfundamentals.org/):&#x20;
4. [WebGPU Fundamentals](https://webgpufundamentals.org/)
