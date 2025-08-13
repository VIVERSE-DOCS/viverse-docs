---
description: Learn about optimizing 3D experiences for WebXR
---

# Optimizing 3D WebXR Experiences

***
VIVERSE experiences run in web browsers. This allows 3D experiences to securely run on almost any hardware, as long as the web browser supports [the WebXR standard](https://developer.mozilla.org/en-US/docs/Web/API/WebXR_Device_API/Fundamentals). This documentation details the challenges and benefits of developing 3D experiences for WebXR.

## Why Build for the Web?

### Portability Across Devices

Web apps reach phones, desktops, and XR headsets via the same URL. Chrome (across all devices it runs on) alone accounts for ~68% of global browsing, making distribution simple.

### Open Standards

WebGPU/WebGL 2, WebXR, WebAssembly (WASM), CSS, and JavaScript are standardized by mature standards bodies and implemented across most major web browser engines. There is no store gatekeeping and no review queues.

### Secure By Default

The web’s application sandbox, HTTPS, and permissions model let you consume powerful graphics, XR, and platform features with user consent and origin isolation—without building and shipping native binaries per operating system and hardware combination.

## The Challenges of Optimizing for WebXR

WebXR is an API that provides developers with the necessary functionality to interface with XR headsets and glasses. This functionality includes rendering and composing 3D content on an XR headset, sensing the movement of the headset and other inputs in the real world, and updating imagery according to the real world data.

This abstracts away complexities like pose estimation and scene understanding. Major 3D rendering engines like Unity, Three.js, PlayCanvas, and Babylon.js directly integrate the WebXR API, making it simple to convert a flat 3D experience into an immersive 3D experience. However, while it is easy to make a functional immersive experience, it is challenging to make an experience that performs well on typical XR hardware in web browsers. We will investigate the unique challenges of developing for XR and developing for web browsers in the next two sections.

## XR Performance Constraints

{% hint style="warning" %}
Maintaining stable performance and high framerates is **extremely** important for WebXR experiences. Many users experience nausea when framerates dip below 60 fps for long periods of time or when a single frame takes too long to present.
{% endhint %}

Developing XR experiences is very rewarding, as it allows users to experience a level of immersion not afforded by flat displays. However, a poorly-optimized experience will break this sense of immersion, even more so than in flat experiences. XR also places the following unique constraints on developers:

- **Higher Performance Thresholds**: A good performance target is a stable 72 frames per second (fps), with minimums of 60 fps. This contrasts with development for PC or Consoles, where 60+ fps is preferred, but users can comfortably play games at 30 fps.
- **Higher Stability Requirements**: It is important to limit screen tearing and dropped frames, as desynchronizations in head tracking or dropped frames can cause confusion and even nausea.
- **Binocular Rendering**: XR experiences are more expesnive to render than flat 3D experiences. This is because the scene needs to be rendered twice: once from the left eye and once from the right eye. While much of the rendering work can be shared (see: [multiview](https://developer.mozilla.org/en-US/docs/Web/API/OVR_multiview2)), there is unavoidable overhead associated with rendering for both eyes.
- **Spatial Tracking Overhead**: In an immersive experience, some portion of each frame is dedicated to updating the real-world position of the headset and the control inputs. This is more expensive than reading inputs in a flat 3D experience, as the headset must multiplex signals from accelerometers, cameras, and other sensors to determine where the user is in the world. This real-world position must then be mapped to a position in the simulated world.
- **Scene Understanding Overhead**: Some WebXR experiences allow interactions between objects in the simulated world and objects in the real world. For example, a virtual tennis ball could bounce off your actual floor. Performing this simulation is expensive, as the device must estimate a 3D collision geometry for the floor, again processing large amounts of sensor data in real time.
- **Mobile-class Hardware**: Most XR headsets run on mobile chipsets like the Qualcomm Snapdragon XR2, which are typically less powerful than those found in desktops, consoles, and laptops (though there is a wide variance in all of these devices). This means that an experience that runs at a stable 60 fps on a mid-range laptop may run at a much lower framerate on an XR headset. It is recommended to [throttle your CPU](https://developer.chrome.com/docs/devtools/settings/throttling/) in your web browser while profiling performance.

## Web Browser Performance Constraints

Developing for web browsers imposes additional constraints in comparison to developing for native platforms (Windows, Mac, iOS, Android, Consoles, etc.). Web browsers guarantee security and portability for 3D experiences by placing some restrictions on the rendering technologies and programming languages that can be used. In order to build a highly optimized WebXR experience, developers need to make the most out of technologies like JavaScript, WebGL2, and WebAssembly. While these technologies are powerful, they have some important limitations compared to the corresponding native technologies.

### WebGL2 Limitations

By default, rendering engines like Three.js, Babylon.js, Unity, and PlayCanvas use the [WebGL 2 API](https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API#webgl_2) to take advantage of hardware-accelerated graphics in the browser. WebGL 2 largely conforms to the Open GL ES 2.0 standard and dispatches commands to the GPU, meaning that the performance of a WebGL API call is very close to the corresponding native OpenGL call. However, there are some limitations to WebGL 2 vs. native code:

- **Dispatching Overhead**: There is overhead when dispatching WebGL calls on the CPU, as the WebGL API call must be translated into the correct native graphics API call [3]. Further, the browser implements more security checks than native code to prevent things like Out of Range Memory Accesses [4]. This slower dispatching limits the number of draw calls that can be performed in the browser.
- **Missing Modern Features**: Because WebGL is based on OpenGL ES 2.0, it lacks many features that modern graphics APIs like DirectX 12, Vulkan, and Metal can provide [5], limiting the theoretical performance of an experience. The WebGPU browser API does support many of these features, but WebXR support is still being specified [6], and WebGPU support is experimental in most major engines [7], [8], [9].

### WebAssembly Limitations

[WebAssembly](https://developer.mozilla.org/en-US/docs/WebAssembly) (or Wasm) is a special binary instruction set that can be executed in all major browsers. Non-browser compatible languages like C++, Rust, and C# can be compiled to this binary format and then run in the browser, often leading to dramatic performance improvements when compared to JavaScript. Real-time 3D engines written in C++ or Rust like Unity and Bevy make use of Wasm to run games in the browser at "near-native speed" [0] . However, there are some limitations compared to native code:

- **Startup Time**: Like all web app source code, Wasm bytecode must be downloaded by the browser before it can begin running. This can result in worse startup time in comparison to native apps, where source is downloaded ahead of time.
- **Garbage Collection**: In Unity WebGL, garbage collection only runs at the end of each frame. This means that allocating many temporary values in a single frame can lead to "temporary quadratic memory growth pressure for the garbage collector" [1].
- **Multi-threading**: Threading support in Wasm is constantly evolving. Although the Wasm supports multi-threading and SIMD instructions, engines must explicitly support these Wasm features. For instance, Unity does not support C# multithreading [2].

### JavaScript Garbage Collection

3D applications running in the browser can be very sensitive to JavaScript Garbage Collection (GC) pauses. [Garbage Collection](https://en.wikipedia.org/wiki/Garbage_collection_(computer_science)) is an automatic memory management technique used in many programming languages, including JavaScript. This technique helps avoid issues like memory leaks and dangling pointers, but has some performance overhead. When memory usage is high in a particular frame, the resulting garbage collection step will freeze the main frame until it completes. In WebXR, this freeze can result in dropped frames, affecting user experience. Although engines like Unity use garbage collection in C# scripting contexts [10], the underlying engine is written in C++, which can take advantage of manual memory management in native contexts. Engines written in JavaScript, like Three.js, do not have this advantage, and developers must be very careful about allocating memory and reusing resources like Vectors and Arrays.

### Network Bandwidth
Startup time is limited by network bandwidth in the browser. This contrasts with native apps, where assets and source code are typically downloaded on the initial install, or in explicit updates to the app. Refer to [Optimizing for the Web](../optimization.md) for more details on optimizing assets for the web.

## Specific Optimization Techniques for WebXR

Given these constraints, how should a developer approach optimizing for WebXR in comparison to optimizing for a native context?

Optimization techniques for WebXR experiences typically fall into 3 buckets:

### 1. Leveraging Browser Technologies

WebXR development optimization requires the use of unique browser APIs, such as WebGL, WebWorkers, and WebAssembly. See the section below for leveraging browser APIs in 3D experiences.

### 2. Optimizing the Scene for the Browser

A developer may need to tailor their experience specifically for mobile chipsets, using lower polygon counts, reducing draw calls, enabling GPU instancing, and reducing the number of dynamic lights in a scene. Assets should be optimized, as they need to be downloaded over the network on application start.

### 3. Engine-specific Optimizations

Many optimization techniques from traditional game development still apply to WebXR engines; for instance, object pooling [11], shader optimization [12], and instancing are still valid techniques. However, WebXR applications require even more optimization because of the higher minimum framerates and lower available compute time. Explicit techniques typically vary for each game engine; for example, object pooling may be more effective in some engines than others. Refer to subsequent pages for specific optimization techniques for major WebXR engines.

## Leveraging Browser APIs in WebXR Experiences

### Multi-threading

The [Web Worker API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API) allows for complex, non-rendering work to be run on a background thread, enabling basic multi-threading. This is particularly useful for computationally expensive tasks like AI pathfinding.

WebGL Rendering can also be performed in a worker thread using the [OffscreenCanvas](https://developer.mozilla.org/en-US/docs/Web/API/OffscreenCanvas) API. This is particularly useful if the main thread is very busy with user interactions and/or animations.

### Caching Assets

The browser exposes two key APIs for caching source code and assets:

- The [Service Worker](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API) API is useful for caching game assets. It acts as a local proxy server between the application and asset CDN, intercepting potentially expensive asset download requests and returning a cached response. This can enable near-native loading performance and can reduce network bandwidth usage for users that may have service-provider imposed data caps.
- [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) is useful for storing large amounts of serializable text data across sessions. This can be used to store game save files and configuration files locally, rather than replicating them to a server: Rendering engines like Babylon [13] and Unity [14] also allow caching assets in IndexedDB for faster loading times.

### WebAssembly

Native engines like Unity compile most of the engine code to Wasm, exposing a thin interoperability layer to JavaScript to interact with user inputs and the WebXR API. In Unity, developers may need to opt into particular Wasm configurations to ensure the best performance:

- [Enabling WebAssembly 2023](https://docs.unity3d.com/6000.1/Documentation/Manual/webassembly-2023.html)
- [Compiling native plug-ins to Wasm](https://docs.unity3d.com/6000.1/Documentation/Manual/webgl-native-plugins-with-emscripten.html)

JavaScript-based engines like Three.js, Babylon.js, and PlayCanvas can leverage Wasm for computationally expensive features like physics simulation, texture decompression, and mesh optimization:

- [MeshOptimizer](https://www.npmjs.com/package/meshoptimizer)
- [Basis Universal](https://github.com/BinomialLLC/basis_universal/blob/master/webgl/encoder/README.md) texture compression
- [Rapier](https://rapier.rs/docs/user_guides/javascript/getting_started_js) physics engine

Code that is not well-suited to JavaScript can also be re-written in a language like C, C++, or Rust and compiled to Wasm using tools like [Emscripten](https://emscripten.org/).

### WebGPU

Though not all engines fully support WebGPU for all rendering tasks, WebGPU can still be leveraged for general purpose GPU computations, allowing non-rendering work to be done on the GPU. This enables highly parallelizable computations like AI pathfinding and animations to be performed asynchronously on the GPU [15] [16].

## Optimizing Scenes for Browsers

In addition to leveraging browser APIs to improve performance, developers must also tailor their experiences towards the browser and the devices that WebXR applications typically run on. In the next pages we will cover engine-specific techniques for implementing these optimizations.

### Reducing and Batching Draw Calls

The first technique to try before lowering the quality of a scene is reducing the number of draw calls per frame. In graphics programming, a draw call is a command sent to the GPU telling it to render a set of triangles. Because GPUs excel at rendering large batches of triangles, it is generally more expensive to generate and submit a draw call on the CPU than it is to execute it on the GPU. As a result, it is advantageous to perform few draw calls with more triangles per draw call than to submit many draw calls with few triangles. In general, draw calls can be reduced by:

- Reusing a single material across different meshes by using atlas textures and texture arrays
- Merging static meshes that use the same material
- Using hardware instancing to draw meshes with the same geometry in a single draw call
- Leveraging level of detail (LOD) systems
- Culling meshes that are not visible

### Reducing Scene Complexity

If draw calls can not be batched further and performance does not match expectations, the developer should consider reducing the complexity of the scene. This can include:

- Reducing the meshes from the scene
- Throttling animation frame rates
- Removing transparency from meshes

### Reducing Visual Fidelity

If the application is GPU bound, i.e. the application spends a significant portion of time on the GPU, the developer should reduce the visual fidelity of the app. This can include:

- Lowering the polygon count of mehses
- Reducing the size of textures
- Reducing the number of dynamic lights in the scene
- Simplifying shaders
- Removing complex post-processing shaders like fog



## By the Numbers: Potential User Reach for Modern 3D Web (Early 2025)

### Internet User Demographics

**Global internet users**:

Experts estimate that there is a Total Addressable Market (TAM) of 5.56 billion people online today [17].

**Emerging Markets**

The following markets have lower internet speeds, but are expected to rapidly grow in the coming decade:

- **South Asia**: 1.03 billion people online [18]
- **Sub-Saharan Africa**: 350 million people online [19] [20]

**Salable Addressable Market**

Of the remaining estimated 4.15 billion people with higher speed internet, an estimated 70% have access to a smartphone capable of rendering high-end 3D web experiences, forming an addressable market of **3 billion users**.

### Browser Demographics for 3D Web

Browser usage demographics heavily favor a WebGPU/WebGL 2 strategy tuned for Chromium first, with good WebGL2 fallback for Safari.


**Global Browser Share** (July 2025) [22]

Overall:
- Chrome ~67.9%,
- Safari ~16.2%,
- Edge ~5.1%,
- Firefox ~2.5%

Mobile:
- Chrome ~67.3%
- Safari ~22.4%

**WebGL 2 Support**

- 99% of iOS devices (going back to iOS 15) support WebGL 2 [21].
- Android Chromium WebGL2 support dates back to 2017 [23]
- In conjunction, a very conservative 90% of the smartphone market supports WebGL 2.

**WebGPU Support**

- Enabled by default as of Chrome 121 released in 2024 [24]
- Experimental support in Safari
- Enabled by default on desktop Chrome as of 2023
- Together, this suggests that 67% of the market has WebGPU access, with more coming online soon with upcoming Safari and FireFox support.


### Internet Bandwidth Readiness

- Mobile speeds are now “3D‑capable” for most users: Global median mobile downlink is ~61.5 Mbps (2025). This is enough for streamed assets, compressed textures, and progressive loading strategies on the open web [25].
- Coverage gaps remain concentrated in the excluded regions: Usage gaps are largest in Sub‑Saharan Africa and South Asia [26]

## Sources

[0] https://developer.mozilla.org/en-US/docs/WebAssembly/Guides/Concepts#what_is_webassembly
[1] https://docs.unity3d.com/2022.3/Documentation/Manual/webgl-memory.html
[2] https://docs.unity3d.com/Manual/webgl-technical-overview.html
[3] https://docs.unity3d.com/6000.3/Documentation/Manual/webgl-performance.html
[4] https://www.khronos.org/webgl/wiki/Main%20Page/cms/security
[5] https://github.com/KhronosGroup/Vulkan-Samples/blob/main/samples/vulkan_basics.adoc
[6] https://github.com/immersive-web/WebXR-WebGPU-Binding/blob/main/explainer.md
[7] https://github.com/mrdoob/three.js/issues/28968
[8] https://doc.babylonjs.com/setup/support/webGPU/webGPUStatus/#features-not-working-because-not-implemented-yet
[9] https://docs.unity3d.com/Manual/WebGPU.html
[10] https://docs.unity3d.com/6000.1/Documentation/Manual/performance-garbage-collector.html
[11] https://en.wikipedia.org/wiki/Object_pool_pattern
[12] https://docs.unity3d.com/6000.1/Documentation/Manual/SL-ShaderPerformance.html
[13] https://doc.babylonjs.com/features/featuresDeepDive/scene/optimizeCached
[14] https://docs.unity3d.com/6000.1/Documentation/Manual/webgl-caching.html
[15] https://surma.dev/things/webgpu/
[16] https://webgpufundamentals.org/webgpu/lessons/webgpu-compute-shaders.html
[17] https://datareportal.com/reports/digital-2025-global-overview-report
[18] https://ngital.com/bangladesh-internet-penetration-2025-data-insights/
[19] https://africa.businessinsider.com/local/lifestyle/african-countries-with-the-largest-internet-population-in-2025/871gpnf
[20] https://www.itu.int/itu-d/reports/statistics/2024/11/10/ff24-internet-use/
[21] https://telemetrydeck.com/survey/apple/iOS/majorSystemVersions/
[22] https://gs.statcounter.com/browser-market-share
[23] https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API#api.webgl2renderingcontext
[24] https://developer.mozilla.org/en-US/docs/Web/API/WebGPU_API#specifications
[25] https://datareportal.com/reports/digital-2025-sub-section-accelerated-access
[26] https://www.gsma.com/r/wp-content/uploads/2024/10/The-State-of-Mobile-Internet-Connectivity-Report-2024.pdf
