---
description: >-
  Learn the fundamentals of browser rendering and techniques for optimizing 3D
  web experiences
---

# The Principles of Web Optimization

***

## Table of Contents

[#the-basics-of-3d-engines-in-the-browser](the-principles-of-web-optimization.md#the-basics-of-3d-engines-in-the-browser "mention")

[#quantifying-the-characteristics-of-good-optimization](the-principles-of-web-optimization.md#quantifying-the-characteristics-of-good-optimization "mention")

[#the-challenges-of-optimizing-for-the-browser](the-principles-of-web-optimization.md#the-challenges-of-optimizing-for-the-browser "mention")

[#optimization-methods-for-the-web](the-principles-of-web-optimization.md#optimization-methods-for-the-web "mention")

***

## The Basics of 3D Engines in the Browser

3D rendering engines are an abstraction around rendering APIs that simplify the process of drawing meshes on the screen and simulating the lighting of the meshes. In the web, rendering engines are composed of:

* A [rendering pipeline](https://www.khronos.org/opengl/wiki/Rendering_Pipeline_Overview) built on top of a browser rendering API, which updates every frame.
* A developer-facing API for initializing and updating meshes, materials, cameras, and lights, written in a browser-supported programming language.
* Abstractions for connecting the output of the rendering pipeline to the browser window.
* Abstractions for updating the scene in response to user inputs received from browser APIs.

<figure><img src="../.gitbook/assets/Render Loop.png" alt="Rendering Image Design: User Input -> State Updates -> Rendering -> Output" width="563"><figcaption></figcaption></figure>

### :art: Core Browser Technologies: WebGL and HTMLCanvas

{% hint style="info" %}
Modern web browsers, such as Safari, Chrome, Firefox, and Edge, implement a set of standardized Graphics APIs and HTML components that collectively support rendering 3D applications in a web page.&#x20;
{% endhint %}

* The primary browser graphics API is [**WebGL**](https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API), which is based on the native OpenGL graphics API. Over the past two years, engines browsers have also begun to support [**WebGPU**](https://developer.mozilla.org/en-US/docs/Web/API/WebGPU_API), a more modern graphics API that integrates tightly with powerful native APIs like [Vulkan](https://www.vulkan.org/), [Metal](https://developer.apple.com/metal/), and [DirectX 12](https://learn.microsoft.com/en-us/windows/win32/directx). The purpose of a graphics API is to provide an interface between the CPU, which constructs an abstract representation of a scene, and the GPU, which renders the abstract scene representation to an image.
* The GPU outputs the rendered image as a list of color values into a special memory allocation known as the [**framebuffer**](https://en.wikipedia.org/wiki/Framebuffer). In order to display the image to the user, the browser provides an HTML component called the [**canvas**](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/canvas). This canvas component occupies a rectangular area of a webpage, and displays the output from the GPU. In practice, the developer constructs a canvas, and then accesses the appropriate graphics API (WebGL or WebGPU) through a reference to the canvas.

### :desktop: Programming Language Support in Web Rendering Engines

{% hint style="info" %}
The only officially supported browser language is [JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript). To execute non-JavaScript code directly in the browser, it must be compiled to a binary format known as [WebAssembly](https://developer.mozilla.org/en-US/docs/WebAssembly), where it can be directly executed by a virtual machine in the browser.
{% endhint %}

There are two main approaches to building rendering engines for the web:

* Compiling an existing engine written in a low-level languages (e.g. C, C++, Rust) to [WebAssembly](https://developer.mozilla.org/en-US/docs/WebAssembly). This approach is taken by engines like [Unity](https://docs.unity3d.com/2020.3/Documentation/Manual/webgl.html), [Godot](https://docs.godotengine.org/en/stable/about/list_of_features.html#platforms), and [Bevy](https://bevy.org/examples/).
* Writing the engine entirely in JavaScript. This approach is used by engines like [PlayCanvas](https://playcanvas.com/), [Three.js](https://threejs.org/), and [Babylon.js](https://www.babylonjs.com/).

Each approach has certain tradeoffs:

<table><thead><tr><th width="142.6875">Tradeoff</th><th>WebAssembly</th><th>JavaScript</th></tr></thead><tbody><tr><td>Browser Compatibility</td><td><a href="https://webassembly.org/docs/web/">Browser APIs are generally not available to the WebAssembly runtime</a>; to interface with the browser, WebAssembly code must call a JavaScript function that then calls the corresponding browser API. Sending commands and data over this reflection layer can be expensive, negating some of the benefits of the theoretically faster low-level WebAssembly code. </td><td>JavaScript can directly call all browser APIs.</td></tr><tr><td>Performance Ceiling</td><td>WebAssembly runs at near-native speeds, enabling a very high performance ceiling.</td><td>While modern JavaScript engines like <a href="https://v8.dev/">V8</a> and <a href="https://docs.webkit.org/Deep%20Dive/JSC/JavaScriptCore.html">JavaScriptCore</a> are capable of running graphics applications, low-level languages like C++ and Rust have an edge in pure performance. In particular, JavaScript is single-threaded, uses automatic memory management, and cannot leverage <a href="https://en.wikipedia.org/wiki/Single_instruction%2C_multiple_data">Single-Instruction Multiple Data</a> parallelization in the browser, putting a theoretical ceiling on performance. Some multi-threading capability is available in the browser via the <a href="the-principles-of-web-optimization.md#reducing-scripting-overhead-with-multi-threading">web worker API</a>.</td></tr><tr><td>Debugging</td><td>WebAssembly debugging support is lacking across major browsers. Developers will often need to rely on debugging tools provided by the engine, making it hard to fix web-specific bugs.</td><td>Profiling and debugging tools are built into the browser, making it relatively easy to identify the root cause of performance problems and bugs.</td></tr><tr><td>Deployment</td><td>WebAssembly requires an additional compilation step in the deployment process. The entire game engine must be compiled with the build.</td><td>JavaScript applications can be deployed directly. Developers can also reduce bundle sizes by stripping unused code and <a href="https://github.com/mishoo/UglifyJS">minifying</a> the JavaScript code.</td></tr></tbody></table>

The choice of engine is not as clear-cut as performance vs. usability. Given the complex nature of rendering engines, it is very difficult to build comprehensive performance benchmarks proving one engine is faster than another. It is more important to choose an engine based on the type of experience you want to build and your strengths as a developer.

### :construction\_site: Building a Rendering Pipeline with WebGL

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

### :camera\_with\_flash: Engine Abstractions: Meshes, Materials, Cameras, and Lights

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

<figure><img src="../.gitbook/assets/Screenshot 2025-08-25 at 7.47.59 PM.png" alt="" width="375"><figcaption></figcaption></figure>

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

### :woman\_technologist: Updating the Scene in Response to User Inputs in the Browser

Generally, users need to interact with the program in some way. The browser exposes a few APIs to allow controlled access to the mouse, keyboard, touch, and gamepad events:

<table><thead><tr><th width="200.3359375">Input Method</th><th>API</th><th>Platform Support</th></tr></thead><tbody><tr><td><strong>Touch</strong></td><td><ul><li><a href="https://developer.mozilla.org/en-US/docs/Web/API/Touch_events">Touch Events API</a>: Responds to swipe, tap, and multitap interactions</li></ul></td><td><ul><li>Mobile</li><li>Chrome, Edge Desktop</li></ul></td></tr><tr><td><strong>Keyboard/Mouse</strong> </td><td><ul><li><a href="https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent">KeyboardEvent API</a>: Responds to key strokes </li><li><a href="https://developer.mozilla.org/en-US/docs/Web/API/MouseEvent">MouseEvent API</a>: Responds to mouse clicks and mouse movements</li></ul></td><td><ul><li>Mobile</li><li>Desktop</li></ul></td></tr><tr><td><strong>Gamepad</strong> </td><td><ul><li><a href="https://developer.mozilla.org/en-US/docs/Web/API/Gamepad_API/Using_the_Gamepad_API">Gamepad API</a>: Detects when controllers are connected, and determines the controller layout</li></ul></td><td><ul><li>Mobile</li><li>Desktop</li></ul></td></tr><tr><td><strong>XR Headset and Inputs</strong></td><td><ul><li><a href="https://developer.mozilla.org/en-US/docs/Web/API/WebXR_Device_API">WebXR API</a>: Provides abstractions for handling headset movement and hand/gamepad inputs.</li></ul></td><td><ul><li>XR</li></ul></td></tr><tr><td>Pointer</td><td><ul><li><a href="https://developer.mozilla.org/en-US/docs/Web/API/Pointer_events">Pointer Events API</a>: Treats mouse, touch, and gamepad events as generic "pointer" events. This simplifies responding to basic click and hover events.</li></ul></td><td><ul><li>Mobile, Desktop, XR</li></ul></td></tr></tbody></table>

### :books: More Learning Resources

General WebGL and WebGPU tutorials:

1. [Canvas Tutorial](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial)&#x20;
2. [Learning WebGL](https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API/By_example)&#x20;
3. [WebGL Fundamentals](https://webglfundamentals.org/):&#x20;
4. [WebGPU Fundamentals](https://webgpufundamentals.org/)

***

## Quantifying the Characteristics of Good Optimization

{% hint style="info" %}
In [introduction-to-optimizing-for-the-web.md](introduction-to-optimizing-for-the-web.md "mention"), we discussed the qualitative characteristics of a well-optimized experience: **fast-loading**, **fluid**, **stable**, **responsive**, **legible**, and **scalable**. In this section, we present quantifiable application metrics and scene metrics, and show how these metrics correlate to a well-optimized scene.&#x20;
{% endhint %}

**Application metrics** are the benchmarks against which we measure optimization, and they are generated by browser and engine profiling tools. **Scene metrics** are the levers that developers use to improve the application metrics. These scene metrics correspond to quantifiable properties from the rendering pipeline described above. **Application metrics** tell us what to optimize, **scene metrics** tell us how to optimize. All of these metrics are moderated by device characteristics and other external factors, which affect the scalability and potential reach of your application.

### Application Metrics to Optimize

<table><thead><tr><th width="199.984375">Metric</th><th width="393.80078125">Description</th><th>Target Values</th></tr></thead><tbody><tr><td><a href="https://developer.android.com/topic/performance/vitals/launch-time#time-initial">Time to initial display</a> (TTID)</td><td>The amount of time (in seconds) that it takes to display the first frame of an application from a cold start. In 3D applications, it is important to show a loading bar as soon as possible. This lets the user know that the app is in a loading state and not frozen.</td><td><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span>: &#x3C; 1.5 s<br><span data-gb-custom-inline data-tag="emoji" data-code="1f7e1">🟡</span>: 1.5 - 4 s<br><span data-gb-custom-inline data-tag="emoji" data-code="274c">❌</span>: > 4 s</td></tr><tr><td><a href="https://developer.android.com/topic/performance/vitals/launch-time#time-full">Time to full display</a> (TTFD)</td><td>The amount of time (in seconds) that it takes for the app to become interactive. For example, this would be the amount of time it takes for the user to gain control of the camera or for clickable UI elements to load. In 3D web applications, it is best to minimize TTFD, as it increases user retention.</td><td><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span>: &#x3C; 10 s<br><span data-gb-custom-inline data-tag="emoji" data-code="1f7e1">🟡</span>: 10 - 30 s<br><span data-gb-custom-inline data-tag="emoji" data-code="274c">❌</span>: > 30 s</td></tr><tr><td><a href="https://web.dev/articles/inp">Interaction to next paint</a> (INP)</td><td>Measures the amount of time (in milliseconds) between a user interaction and the next frame. This tracks the direct scripting overhead of user interactions. This metric is most relevant to interactions that incur asset loading, shader compilation, or extensive data processing, like entering a new area of the world, as these interactions can cause the app to momentarily freeze.</td><td><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span>: &#x3C; 16 ms<br><span data-gb-custom-inline data-tag="emoji" data-code="1f7e1">🟡</span>: 16 - 75 ms<br><span data-gb-custom-inline data-tag="emoji" data-code="274c">❌</span>: > 75 ms</td></tr><tr><td><a href="https://en.wikipedia.org/wiki/Frame_rate">Frame rate, or frames per second</a> (FPS)</td><td>The frequency at which frames are rendered to the screen. Applications with higher FPS appear more fluid, while applications with low FPS can look like a slideshow, making it difficult for the user to control their character.</td><td>Mobile &#x26; Desktop<br><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span>: > 60 fps<br><span data-gb-custom-inline data-tag="emoji" data-code="1f7e1">🟡</span>: 45 - 60 fps<br><span data-gb-custom-inline data-tag="emoji" data-code="274c">❌</span>: &#x3C; 45 fps<br><br>XR: <br><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span>: > 72 fps<br><span data-gb-custom-inline data-tag="emoji" data-code="1f7e1">🟡</span>: 60 - 72 fps<br><span data-gb-custom-inline data-tag="emoji" data-code="274c">❌</span>: &#x3C; 60 fps</td></tr><tr><td><a href="https://www.capframex.com/blog/post/Explanation%20of%20different%20performance%20metrics">5th percentile minimum frame rate</a> (5th %ile FPS)</td><td>High variance in frame time can result in a poor user experience, as this can be perceived as "choppiness" and break immersion. To get a sense of how much choppiness there is an application, we can take the 5th percentile value from <em>n</em> samples of the frame rate. In a stable application, this value will be around 80% of the median FPS.</td><td>Mobile &#x26; Desktop<br><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span>: > 45 fps<br><span data-gb-custom-inline data-tag="emoji" data-code="1f7e1">🟡</span>: 30 - 45 fps<br><span data-gb-custom-inline data-tag="emoji" data-code="274c">❌</span>: &#x3C; 30 fps<br><br>XR: <br><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span>: > 60 fps<br><span data-gb-custom-inline data-tag="emoji" data-code="1f7e1">🟡</span>: 45 - 60 fps<br><span data-gb-custom-inline data-tag="emoji" data-code="274c">❌</span>: &#x3C; 45 fps</td></tr><tr><td><a href="https://en.wikipedia.org/wiki/Image_quality">Image Quality</a></td><td>Indicates how well the real-time rendered scene matches an idealized version of the scene. A heuristic for this metric is the amount of resolution downscaling that is applied to the canvas relative to the screen. This is described more below. </td><td><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span>: 100% scaling<br><span data-gb-custom-inline data-tag="emoji" data-code="1f7e1">🟡</span>: 85 - 100% scaling<br><span data-gb-custom-inline data-tag="emoji" data-code="274c">❌</span>: &#x3C; 85% scaling</td></tr><tr><td>Memory Usage</td><td>Higher memory usage (in megabytes/MB) generally correlates to poorer performance on lower-end devices, which typically have less available system memory (<a href="https://en.wikipedia.org/wiki/Random-access_memory">RAM</a>) and smaller CPU caches. Higher memory usage also correlates to higher <a href="https://en.wikipedia.org/wiki/Garbage_collection_(computer_science)">garbage collection</a> overhead, which can cause the scene to momentarily freeze. If too much memory is used, the browser tab will crash.</td><td><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span>: &#x3C; 800 MB<br><span data-gb-custom-inline data-tag="emoji" data-code="1f7e1">🟡</span>: 800 - 1600 MB<br><span data-gb-custom-inline data-tag="emoji" data-code="274c">❌</span>: > 1600 MB</td></tr></tbody></table>

### Scene Metrics to Optimize

<table><thead><tr><th width="200.12890625">Metric</th><th>Description</th></tr></thead><tbody><tr><td>Asset sizes</td><td>By decreasing the file size of static assets (models, textures, code), users can improve the loading time of the application (TTID, TTFD) and time spent loading new areas. Asset sizes can be improved by <a href="https://docs.blender.org/manual/en/latest/modeling/modifiers/generate/decimate.html">decimating meshes</a>, <a href="https://developer.chrome.com/docs/lighthouse/performance/uses-responsive-images">reducing texture size</a>, and <a href="https://www.geeksforgeeks.org/javascript/how-to-minify-javascript/">minifying code</a>.</td></tr><tr><td><a href="https://webglfundamentals.org/webgl/lessons/webgl-resizing-the-canvas.html">Framebuffer resolution and scale</a></td><td><p>There are 3 types of resolution that impact image quality: <strong>framebuffer resolution</strong>, <strong>window resolution</strong>, and <strong>device resolution</strong>. The <strong>framebuffer resolution</strong> of an application is the number of pixels that are output into the framebuffer, represented as a (width, height) pair. This is distinct from the <strong>window resolution</strong>, which is the number of <a href="https://developer.mozilla.org/en-US/docs/Glossary/CSS_pixel">CSS pixels</a> displayed to the user. <strong>Framebuffer scale</strong> represents the ratio of framebuffer resolution divided by window resolution, where higher values represent a more legible image. Lowering framebuffer scale can increase FPS at the expense of image quality. </p><p></p><p><strong>Device resolution</strong> is the number of <a href="https://developer.mozilla.org/en-US/docs/Glossary/Device_pixel">physical pixels</a> in the screen of the device. On devices with high pixel density, multiple physical pixels will make up a single CSS pixel, resulting in a higher quality image. The ratio of phyiscal pixels to CSS pixels is measured by the <a href="https://developer.mozilla.org/en-US/docs/Web/API/Window/devicePixelRatio">devicePixelRatio</a> API.</p></td></tr><tr><td>Texture resolution</td><td>The resolution of a texture dictates how legible corresponding models, text, and shader effects are in the application. Reducing texture resolution can increase FPS by decreasing image quality. This is distinct from the downloaded texture size, as high-resolution textures can be downsampled at runtime, providing an option for runtime scalability.</td></tr><tr><td><a href="https://toji.dev/webxr-scene-optimization/#reducing-draw-calls">Draw call count</a></td><td>In 3D applications, <a href="introduction-to-optimizing-for-the-web.md#optimize-scenes-for-the-browser">draw calls</a> tend to be the primary bottleneck. <a href="https://howik.com/understanding-draw-calls">Draw calls are very CPU intensive</a>, so FPS inversely scales with the number of draw calls in the scene. To reduce draw calls, developers can reuse materials across meshes, merge static meshes, and use instancing to clone meshes.</td></tr><tr><td>Scripting overhead</td><td>The amount of time spent each frame running application logic (in milliseconds). In a 3D experience, some time is spent each frame transforming meshes, responding to user inputs, and updating animations. Developers must optimize this per-frame scripting time to be as small as possible to ensure higher FPS. Intermittent scripting times increases are also a primary contributor to low 5th percentile minimums, as lots of application logic may need to be run when entering a new area or processing a large amount of incoming data.</td></tr><tr><td><a href="https://docs.unity3d.com/6000.0/Documentation/Manual/SL-ShaderPerformance.html">Shader overhead</a></td><td>Time spent in shaders is difficult to measure directly, but it can be inferred from the total amount of time spent on the GPU. To reduce shader time, developers can remove expensive functions (pow, log, sin), use built-in functions, use lower precision variables, and avoid repeated calculations.</td></tr></tbody></table>

### External Factors Affecting Scalability

Application metrics are affected by factors outside of a developer's control. In particular, a developer cannot control the hardware specifications (CPU + GPU + RAM) or internet connection that the experience will run on, making it difficult to optimize the experience for all users. Developers should aim to provide a good experience on the median hardware specs for each platform (mobile, desktop, XR) that their experience supports and adjust asset sizes according to median network bandwidth. A good approximation for this is to target a flagship device that is a few years old (an iPhone 12 or Galaxy s20 in 2025), and to expect around 50 mbps network bandwidth.

{% tabs %}
{% tab title="Device Class" %}
3D capable devices are typically split into 3 categories: Mobile (Android and iOS tablets and phones), Desktop (laptop and desktop computers), and recently XR (augmented and virtual reality headsets). Desktops are  the most powerful, and can render experiences at higher fidelity than mobile devices and XR headsets. If you plan to support multiple platforms, expect to optimize code and assets for each platform.
{% endtab %}

{% tab title="CPU" %}
A [CPU's](https://en.wikipedia.org/wiki/Central_processing_unit) speed is determined by three main factors: **clock speed**, core **count**, and **cache size**. Clock speed determines how fast an individual function can complete on a CPU, higher clock speeds corresponding to faster execution. Higher cache sizes allow the CPU to access data very quickly, avoiding relatively expensive RAM operations. However, leveraging cache size often relies on careful optimization. Core count determines how much parallelism an application can leverage. A program can theoretically cut scripting time proportionally to the number of cores, but this requires additional optimization with Web Workers or WebAssembly. Increasing CPU speed allows for more complex scripting and higher draw call counts. Loading times also tend to increase, as assets are processed more quickly once they are downloaded.
{% endtab %}

{% tab title="GPU" %}
A [GPU's](https://en.wikipedia.org/wiki/Graphics_processing_unit) speed is determined by memory size (**VRAM**), **GPU clock speed**, and **core count**. For GPUs, core count and memory size are more important than clock speed, as GPUs have to perform many operations in parallel. Having more cores and more memory allow GPUs to process larger textures and more vertices with higher precision. Increased clock speeds typically decrease the amount of time spent in shaders, though this is typically only a bottleneck with very expensive shaders.
{% endtab %}

{% tab title="RAM" %}
The amount of system memory, or [RAM](https://en.wikipedia.org/wiki/Random-access_memory), that a user's device has will limit the types of experience that they are able to run. RAM is used to keep active program data readily available, so more complex experiences will require more RAM. Typical mobile devices will have 1GB - 8GB of RAM, which is enough to run smaller experiences with good performance. Desktop and laptop computers typically have 8GB - 32GB of RAM, which is enough to run medium experiences. The highest-quality experiences require 16+ GB of available RAM.
{% endtab %}

{% tab title="Network" %}
A user's network speed is out of a developer's control. If a user does has less than 1 MB/s internet speed, they will have trouble accessing 3D experiences in a reasonable time. Nevertheless, developers should tune asset sizes such that users with a _wide_ range of internet speeds can access their experience. Developers can also take use of progressive loading to download assets throughout the experience runtime.
{% endtab %}
{% endtabs %}

### Correlating Qualitative Characteristics to Metrics

<table><thead><tr><th width="176.875" valign="top">Characteristic</th><th valign="top">Application Metrics</th><th valign="top">Scene Metrics</th><th valign="top">External Factors</th></tr></thead><tbody><tr><td valign="top">Fast Loading</td><td valign="top"><ul><li>TTID</li><li>TTFD</li><li>INP</li></ul></td><td valign="top"><ul><li>Asset Sizes</li></ul></td><td valign="top"><ul><li>Network Speed</li><li>CPU Clock Speed</li></ul></td></tr><tr><td valign="top">Fluid</td><td valign="top"><ul><li>FPS</li></ul></td><td valign="top"><ul><li>Draw Call Count</li><li>Framebuffer Resolution</li><li>Scripting Overhead</li><li>Shader Overhead</li><li>Texture Resolution</li></ul></td><td valign="top"><ul><li>CPU Clock Speed</li><li>GPU VRAM</li><li>GPU Clock Speed</li></ul></td></tr><tr><td valign="top">Stable</td><td valign="top"><ul><li>5th Percentile Frame Rate</li><li>Memory Usage</li></ul></td><td valign="top"><ul><li>95th percentile shader overhead</li></ul><ul><li>95th percentile scripting overhead</li></ul><ul><li>95th percentile draw call count</li></ul></td><td valign="top"><ul><li>RAM</li><li>CPU Clock Speed</li><li>GPU VRAM</li><li>GPU Clock Speed</li></ul></td></tr><tr><td valign="top">Responsive</td><td valign="top"><ul><li>Interaction to Next Paint (INP)</li></ul></td><td valign="top"><ul><li>Maximum scripting overhead</li></ul><ul><li>Maximum shader overhead</li></ul><ul><li>Maximum draw call count</li></ul></td><td valign="top"><ul><li>CPU Clock Speed</li></ul></td></tr><tr><td valign="top">Legible</td><td valign="top"><ul><li>Image Quality</li></ul></td><td valign="top"><ul><li>Texture resolution</li><li>Framebuffer scaling</li></ul></td><td valign="top"><ul><li>Screen Resolution</li><li>Screen Size</li></ul></td></tr><tr><td valign="top">Scalable</td><td valign="top"><ul><li>For scalability, validate all application metrics on each device class you plan to support (mobile, XR, desktop)</li></ul></td><td valign="top"><ul><li>Texture resolution may need to be tuned for each device</li></ul><ul><li>Draw call count may need to be tuned for each device</li></ul></td><td valign="top"><ul><li>Device Classes Supported</li></ul></td></tr></tbody></table>

***

## The Challenges of Optimizing for the Browser

The browser makes certain tradeoffs that make it difficult to achieve the target application metrics laid out in the previous section. In particular, the browser prioritizes security over performance, making it harder to achieve FPS targets on mobile and XR platforms. Additionally, loading time is dependent on network speed, since applications are re-downloaded on every launch. This section seeks to identify common problems developers must overcome when developing for browsers.

### :scales: Performance Differences between WebGL and Native Rendering APIs

By default, rendering engines like Three.js, Babylon.js, Unity, and PlayCanvas use the [WebGL 2 API](https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API#webgl_2) to take advantage of hardware-accelerated graphics in the browser. WebGL 2 largely conforms to the Open GL ES 2.0 standard and dispatches commands to the GPU, meaning that the performance of a WebGL API call is very close to the corresponding native OpenGL call. However, there are some limitations to WebGL 2 in comparison to native graphics APIs:

* **Dispatching Overhead**: There is [overhead when dispatching WebGL calls on the CPU](https://docs.unity3d.com/6000.3/Documentation/Manual/webgl-performance.html), as the WebGL API call must be translated into the correct native graphics API call. Further, the browser implements more security checks than native code to prevent things like [Out of Range Memory Accesses](https://www.khronos.org/webgl/wiki/Main%20Page/cms/security). This slower dispatching limits the number of draw calls that can be performed in the browser.
* **Missing Modern Features**: Because WebGL is based on OpenGL ES 2.0, it lacks many features that modern graphics APIs like DirectX 12, Metal, and [Vulkan can provide](https://github.com/KhronosGroup/Vulkan-Samples/blob/main/samples/vulkan_basics.adoc), limiting the theoretical performance of an experience. The WebGPU browser API does support many of these features, but WebGPU support is experimental in Safari and Firefox. Major engines like [Three.js](https://github.com/mrdoob/three.js/issues/28968), [Babylon.js](https://doc.babylonjs.com/setup/support/webGPU/webGPUStatus/#features-not-working-because-not-implemented-yet), [PlayCanvas](https://blog.playcanvas.com/initial-webgpu-support-lands-in-playcanvas-engine-1-62/), and [Unity](https://docs.unity3d.com/Manual/WebGPU.html) provide WebGPU support, though support for the [WebXR API in WebGPU](https://github.com/immersive-web/WebXR-WebGPU-Binding/blob/main/explainer.md) is still being standardized.

### :brain: Memory Management in 3D Web Applications

3D applications running in the browser can be very sensitive to JavaScript Garbage Collection (GC) pauses. [Garbage Collection](https://en.wikipedia.org/wiki/Garbage_collection_\(computer_science\)) is an automatic memory management technique used in many programming languages, including JavaScript. This technique helps avoid issues like memory leaks and dangling pointers, but has some performance overhead. When memory usage is high in a particular frame, the resulting garbage collection step will freeze the main frame until it completes. In Web applications, this freeze can result in dropped frames, affecting user experience.&#x20;

Although engines like Unity use garbage collection in [C# scripting contexts](https://docs.unity3d.com/6000.1/Documentation/Manual/performance-garbage-collector.html), the underlying engine is written in C++, which can take advantage of manual memory management in native contexts. Engines written in JavaScript, like Three.js, do not have this advantage, and developers must be very careful about allocating memory and reusing resources like Vectors and Arrays.

#### Why is Garbage Collection Expensive?

Modern browsers use the "mark-and-sweep" algorithm to implement garbage collection. This algorithm is synchronous and scales linearly with the amount of memory in use. As a result, the app will momentarily freeze while cleaning up a large amount of unused memory. The mark-and-sweep algorithm consists of two phases: a depth-first search of memory usage that marks all reachable objects and a sweep pass that releases all unreachable memory. The mark-and-sweep algorithm works like this:&#x20;

```javascript
// A global tree structure that contains links to reachable objects
let root = {} 

// A global data structure containing a reference to all allocated objects
let jsHeap = []

function mark(node) {
    if (!node.marked) {
        node.marked = true
        
        for (const referencedNode of node.references) {
            mark(referencedNode)
        }
    }
}

function sweep() {
    for (const node of jsHeap) {
        if (node.marked) {
            node.marked = false
        } else {
            jsHeap.release(node)
        }
    }
}

function garbageCollect() {
    mark(root)
    sweep()
}
```

This approach prevents memory leaks and dangling references, but has overhead in comparison to manual memory management techniques. In manual memory management, the allocated objects would be freed when they are no longer used, removing the need for a separate pass over all of the objects in the tree.

### :zap: App Startup Time

Startup time is limited by network bandwidth in the browser. This contrasts with native apps, where assets and source code are typically downloaded on the initial install, or in explicit updates to the app.&#x20;

Research show that bounce rates [increase by 123% if an application takes more than 10 seconds to load](https://www.tooltester.com/en/blog/website-loading-time-statistics/), and 53% of users will leave a webpage if it takes more than 3 seconds to load.  Given that worldwide network bandwidth is conservatively [50 mbps](https://www.speedtest.net/global-index#mobile), developers should aim for JavaScript bundle sizes under 5 MB to achieve a 1.5 second TTID. To achieve a TTFD under 10 seconds, developers should aim for 40 MB or less of total assets (code, textures, audio, and models).

Refer to [Optimizing for the Web](../optimization.md) for more details on optimizing assets for the web.&#x20;

### :mobile\_phone: Optimizing for Mobile Devices

Web development is distinct from native development because the same build runs on all devices, meaning a single build must support mobile and desktop platforms. This presents the following challenges to developers:

* **Mobile devices have less powerful hardware than desktop computers**: This means that experiences that run at 60 FPS on desktop computers may require additional optimization to run on mobile phones at 60 FPS.
* **Builds must scale performance automatically**: This means that your application may need to provide multiple scene qualities that it can fall back to, and multiple asset configurations that can be selected from at runtime.
* **Mobile devices can have a wide range of screen sizes**: This makes choosing texture size and font size more challenging, as a user on a tablet expects a different experience from a user on a phone. Developers must detect the window size and device pixel ratio at runtime to ensure that the correct texture sizes and font sizes are used.

### :goggles: Optimizing for XR Devices

The browser enables XR applications through the [WebXR API](https://developer.mozilla.org/en-US/docs/Web/API/WebXR_Device_API/Fundamentals), meaning that a mobile or desktop experience can run on an XR headset with some additional work. Developing XR experiences is rewarding, as it allows users to experience a level of immersion not afforded by flat displays. However, it may take a lot of work to properly optimize a 3D web build for WebXR. WebXR development places the following unique constraints on developers:

* **Higher FPS Thresholds**: A good performance target is a [stable 72 frames per second](https://developers.meta.com/horizon/resources/vrc-quest-performance-1) (fps), with minimums of 60 fps. This contrasts with development for PC or Consoles, where 60+ fps is preferred, but users can comfortably play games at 30 fps.
* **Higher Stability Requirements**: It is important to limit screen tearing and dropped frames, as desynchronization in head tracking or dropped frames can cause nausea.
* **Binocular Rendering**: XR experiences are more expensive to render than flat 3D experiences. This is because the scene needs to be [rendered twice](https://developer.mozilla.org/en-US/docs/Web/API/WebXR_Device_API/Rendering#the_optics_of_3d): once from the left eye and once from the right eye. While much of the rendering work can be shared (see: [multiview](https://developer.mozilla.org/en-US/docs/Web/API/OVR_multiview2)), there is unavoidable overhead associated with rendering for both eyes.
* **Spatial Tracking Overhead**: In an immersive experience, some portion of each frame is dedicated to [updating the real-world position of the headset](https://developer.mozilla.org/en-US/docs/Web/API/WebXR_Device_API/Spatial_tracking) and the control inputs. This is more expensive than reading inputs in a flat 3D experience, as the headset must multiplex signals from accelerometers, cameras, and other sensors to determine where the user is in the world. This real-world position must then be mapped to a position in the simulated world.
* **Scene Understanding Overhead**: Some WebXR experiences allow interactions between objects in the simulated world and objects in the real world. For example, a virtual tennis ball could bounce off your actual floor. Performing this simulation is expensive, as the device must estimate a 3D collision geometry for the floor, again processing large amounts of sensor data in real time.
* **Mobile-class Hardware**: Most XR headsets run on mobile chipsets like the [Qualcomm Snapdragon XR2 Gen 2](https://www.qualcomm.com/products/mobile/snapdragon/xr-vr-ar/snapdragon-xr2-gen-2-platform), which are typically less powerful than those found in desktops, consoles, and laptops (though there is a wide variance in all of these devices). This means that an experience that runs at a stable 60 fps on a mid-range laptop may run at a much lower framerate on an XR headset. It is recommended to [throttle your CPU](https://developer.chrome.com/docs/devtools/settings/throttling/) in your web browser while profiling performance.

***

## Optimization Methods for the Web

We recommend the following approach to optimizing an application for the web:

{% stepper %}
{% step %}
#### Profile the Application

Before doing any optimization work, be sure to profile your application in the browser on each of the devices you support. If you do not have access to a particular device, most desktop browsers allow you to emulate other devices. Once you've generated application metrics, use built-in engine tools to profile your scene. Together, these metrics should give you the appropriate direction for what to optimize, whether it be scripting performance, draw calls, or shader performance. In [profiling-and-testing-3d-web-experiences.md](profiling-and-testing-3d-web-experiences.md "mention"), we provide resources for profiling specific browsers, engines, and devices.
{% endstep %}

{% step %}
#### Perform Engine-Specific Optimizations

Many optimization techniques from traditional game development still apply to Web engines; for instance, [object pooling](https://en.wikipedia.org/wiki/Object_pool_pattern), [shader optimization](https://docs.unity3d.com/6000.1/Documentation/Manual/SL-ShaderPerformance.html), and instancing are still valid techniques. However, explicit techniques typically vary for each game engine; for example, object pooling may be more effective in some engines than others. Refer to [profiling-and-testing-3d-web-experiences.md](profiling-and-testing-3d-web-experiences.md "mention") for specific optimization techniques for major web engines.
{% endstep %}

{% step %}
#### Optimize the Scene for the Browser

A developer may need to tailor their experience specifically for mobile chipsets, using lower polygon counts, reducing draw calls, enabling GPU instancing, removing unnecessary shadows on dynamic lights, adding lightmaps, and reducing the number of dynamic lights in a scene. Assets should be optimized, as they need to be downloaded over the network on application start.
{% endstep %}

{% step %}
#### Leverage Browser Technologies to Improve Performance

Web development optimization requires the use of unique browser APIs, such as WebGL, WebWorkers, and WebAssembly. See the section below for leveraging browser APIs in 3D experiences.
{% endstep %}
{% endstepper %}

### :city\_dusk: Optimizing Scenes for Browsers

In addition to leveraging browser APIs to improve performance, developers must also tailor their experiences towards the browser and the devices that web applications typically run on. In the next pages we will cover engine-specific techniques for implementing these optimizations.

#### Reducing and Batching Draw Calls

The first technique to try before lowering the quality of a scene is reducing the number of draw calls per frame. In general, draw calls can be reduced with the following techniques:

* Reusing a single material across different meshes by using a [texture atlas](https://en.wikipedia.org/wiki/Texture_atlas) or [array textures](https://www.khronos.org/opengl/wiki/Array_Texture). Implementation details typically vary by engine.
* Merging static meshes that use the same material into a single material. This [blog post](https://toji.dev/webxr-scene-optimization/#merging-by-material) provides a detailed example of how this can be done in asset creation tools like [Blender](https://www.blender.org/download/).
* Using [hardware instancing](https://webglfundamentals.org/webgl/lessons/webgl-instanced-drawing.html) to draw meshes with the same geometry in a single draw call.
* Leveraging [level of detail](https://en.wikipedia.org/wiki/Level_of_detail_\(computer_graphics\)) (LOD) systems.
* Culling meshes that are not visible with [occlusion queries](https://www.khronos.org/opengl/wiki/Query_Object#Occlusion_queries).

#### Reusing Objects to Reduce Memory Usage

In experiences with high memory usage, it is important to reuse allocated memory objects and hardware objects wherever possible. As an example, a developer may want to avoid reallocating vector objects in a loop, as this can drastically increase the chance of a GC pause.

:x: Without Reuse: 1 million vector objects are created, all of which must be cleaned up by the garbage collector

```javascript
const positionBuffer = mesh.getVertexBuffer('position');
for (let x = 0; x < 1000; x++) {
    for (let y = 0; y < 1000; y++) {
        const position = vec3(x, y, 0);
        position.copyTo(positionBuffer, 3 * (x * 1000 + y);
    }
}
```

:white\_check\_mark: With Reuse: A single temporary vector is created, minimizing garbage collector overhead

```javascript
const positionBuffer = mesh.getVertexBuffer('position');
const tempPosition = vec3(0, 0, 0);
for (let x = 0; x < 1000; x++) {
    for (let y = 0; y < 1000; y++) {
        tempPosition.set(x, y, 0);
        tempPosition.copyTo(positionBuffer, 3 * (x * 1000 + y);
    }
}
```

#### Reducing Scene Complexity

If draw calls can not be batched further and performance does not match expectations, the developer should consider reducing the complexity of the scene. This can include:

* Reducing the number of meshes in the scene.
* Removing transparency from meshes.
* Removing reflections from the scene.
* Using static [lightmaps](https://en.wikipedia.org/wiki/Lightmap) instead of dynamic lights where possible.

#### Reducing Visual Fidelity

If the application is GPU bound, i.e. the application spends a significant portion of time on the GPU, the developer should reduce the visual fidelity of the app. This can include:

* Optimizing meshes with tools like [meshoptimizer](https://meshoptimizer.org/).
* Reducing the size of textures.
* Reducing the number of dynamic lights in the scene.
* Removing shadows from dynamic lights that do not need them.
* Simplifying mesh materials by removing expensive [physically-based rendering](https://en.wikipedia.org/wiki/Physically_based_rendering) effects.
* Removing complex post-processing shaders like fog.

### :person\_lifting\_weights: Improving Performance By Leveraging Browser APIs

#### Improving Load Times By Caching Assets

Although a user will always need to download assets on the first launch of a 3D web experience, the browser exposes two key APIs for caching source code and assets, making subsequent load times nearly instantaneous:

* The [Service Worker](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API) API is useful for caching game assets. It acts as a local proxy server between the application and asset CDN, intercepting potentially expensive asset download requests and returning a cached response. This can enable near-native loading performance and can reduce network bandwidth usage for users that may have service-provider imposed data caps.
* [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) is useful for storing large amounts of serializable text data across sessions. This can be used to store game save files and configuration files locally, rather than replicating them to a server: Rendering engines like [Babylon](https://doc.babylonjs.com/features/featuresDeepDive/scene/optimizeCached) and [Unity](https://docs.unity3d.com/6000.1/Documentation/Manual/webgl-caching.html) also allow caching assets in IndexedDB for faster loading times.

#### Reducing Scripting Overhead with Multi-Threading

The [Web Worker API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API) allows for complex, non-rendering work to be run on a background thread, enabling basic multi-threading. This is particularly useful for computationally expensive tasks like AI pathfinding. If an app spends too much time running application logic, this is a useful browser API to leverage.

WebGL Rendering can also be performed in a worker thread using the [OffscreenCanvas](https://developer.mozilla.org/en-US/docs/Web/API/OffscreenCanvas) API. This is particularly useful if the main thread has scripting overhead resulting from user interactions and/or animations that cannot be moved to a background thread.

#### Reducing Scripting Overhead with WebAssembly

[WebAssembly](https://developer.mozilla.org/en-US/docs/WebAssembly) (or Wasm) is a special binary instruction set that can be executed in all major browsers. Non-browser compatible languages like C++, Rust, and C# can be compiled to this binary format and then run in the browser, leading to dramatic performance improvements when compared to JavaScript in specific tasks. Real-time 3D engines written in C++ or Rust like Unity and Bevy make use of Wasm to run games in the browser at ["near-native speed"](https://developer.mozilla.org/en-US/docs/WebAssembly/Guides/Concepts#what_is_webassembly).

JavaScript-based engines like Three.js, Babylon.js, and PlayCanvas can leverage Wasm for computationally expensive features like physics simulation, texture decompression, and mesh optimization, reducing scripting overhead in application logic:

* [MeshOptimizer](https://www.npmjs.com/package/meshoptimizer)
* [Basis Universal](https://github.com/BinomialLLC/basis_universal/blob/master/webgl/encoder/README.md) texture compression
* [Rapier](https://rapier.rs/docs/user_guides/javascript/getting_started_js) physics engine
* First-party native (C, C++, Rust) code that is not well-suited to JavaScript can be compiled to Wasm using tools like [Emscripten](https://emscripten.org/).

There are some limitations to Wasm in comparison to native code:

* **Startup Time**: Like all web app source code, Wasm bytecode must be downloaded by the browser before it can begin running. This can result in worse startup time in comparison to native apps, where source is downloaded ahead of time.
* **Garbage Collection**: In Unity WebGL, garbage collection only runs at the end of each frame. This means that allocating many temporary values in a single frame can lead to ["temporary quadratic memory growth pressure for the garbage collector"](https://docs.unity3d.com/2022.3/Documentation/Manual/webgl-memory.html).
* **Multi-threading**: Threading support in Wasm is constantly evolving. Although the Wasm supports multi-threading and SIMD instructions, engines must explicitly support these Wasm features. For instance, [Unity does not support C# multithreading](https://docs.unity3d.com/Manual/webgl-technical-overview.html).
* WebAssembly does not always guarantee the [best performance](https://ianjk.com/webassembly-vs-javascript/). It may be more cost-effective to optimize existing JavaScript code rather than introducing Wasm into your project.

#### Reducing Scripting Overhead with WebGPU Compute Shaders

WebGPU is gaining adoption as a potential substitute for WebGL in the browser, providing a direct abstraction for modern rendering APIs like Vulkan, Metal, and DirectX 12. While [WebGPU](https://developer.mozilla.org/en-US/docs/Web/API/WebGPU_API) can be used as the primary rendering API in some engines, it can also be leveraged in WebGL-based applications to run [compute shaders](https://webgpufundamentals.org/webgpu/lessons/webgpu-compute-shaders.html) allowing non-rendering work to be done on the GPU. This enables highly parallelizable computations like AI pathfinding and animations to be performed asynchronously [on the GPU](https://surma.dev/things/webgpu/). Parallelizing these computations can reduce scripting overhead and improve frame rates.
