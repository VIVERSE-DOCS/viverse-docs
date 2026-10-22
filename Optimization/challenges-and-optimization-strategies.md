---
description: >-
  Learn about the fundamental difficulties of optimizing in the browser and how
  to address them.
icon: hand-fist
---

# Challenges and Optimization Strategies

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

Refer to [Optimizing for the Web](/broken/pages/Oy7laS7LpHitbo51HRhg) for more details on optimizing assets for the web.&#x20;

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

Before doing any optimization work, be sure to profile your application in the browser on each of the devices you support. If you do not have access to a particular device, most desktop browsers allow you to emulate other devices. Once you've generated application metrics, use built-in engine tools to profile your scene. Together, these metrics should give you the appropriate direction for what to optimize, whether it be scripting performance, draw calls, or shader performance. In [profiling-in-the-browser.md](profiling-in-the-browser.md "mention"), we provide resources for profiling specific browsers, engines, and devices.
{% endstep %}

{% step %}
#### Perform Engine-Specific Optimizations

Many optimization techniques from traditional game development still apply to Web engines; for instance, [object pooling](https://en.wikipedia.org/wiki/Object_pool_pattern), [shader optimization](https://docs.unity3d.com/6000.1/Documentation/Manual/SL-ShaderPerformance.html), and instancing are still valid techniques. However, explicit techniques typically vary for each game engine; for example, object pooling may be more effective in some engines than others. Refer to [overview-of-3d-web-rendering-engines.md](overview-of-3d-web-rendering-engines.md "mention") for specific optimization techniques for major web engines.
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
