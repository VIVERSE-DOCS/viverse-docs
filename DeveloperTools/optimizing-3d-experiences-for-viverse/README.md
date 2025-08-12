---
description: Learn about optimizing 3D experiences for WebXR and VIVERSE
---

# Optimizing 3D Web Experiences for VIVERSE

***
VIVERSE experiences run in web browsers. This allows 3D experiences to securely run on almost any hardware, as long as the browser on that device supports [the WebXR standard](https://developer.mozilla.org/en-US/docs/Web/API/WebXR_Device_API/Fundamentals). This documentation details the challenges and benefits of developing 3D experiences with WebXR as the target.

## The Challenges of Optimizing for WebXR

WebXR is an API that provides the necessary functionality for rendering VR and AR (collectively referred to as XR) content to an XR headset and sensing real-world updates to the headset and other inputs (controllers, hands). This abstracts away complexities like pose estimation and scene understanding. Major 3D rendering engines like Unity, Three.js, PlayCanvas, and Babylon.js have further wrappers around WebXR that make it very easy to convert a traditional 3D experience into an immersive 3D experience. However, while it is easy to make a functional experience  in XR, it is challenging to make an experience that performs well on an XR device. We will investigate these unique challenges in the following section.

### XR Performance Constraints

1. **Higher performance thresholds**: The most important thing to keep in mind when developing an XR experience is that people will get nauseous if performance drops below a certain threshold. A good performance target is a stable 72 frames per second (fps), with minimums of 60 fps. This contrasts with development for PC or Consoles, where 60+ fps is preferred, but users can comfortably play games at 30 fps.
2. **Stability**: It is important to limit screen tearing and dropped frames, as desynchronizations in head tracking and controller or hand tracking can also cause nausea.
3. **Binocular Rendering**: XR experiences are more expesnive to render than traditional 3D experiences. This is because the scene effectively needs to be rendered twice: once from the left eye and once from the right eye. While much of the rendering work can be shared (see: [multiview](https://developer.mozilla.org/en-US/docs/Web/API/OVR_multiview2)), there is some unavoidable overhead associated with rendering for both eyes.
4. **Spatial Tracking Overhead**: In an immersive experience, some portion of each frame is dedicated to updating the real-world position of the headset and the control inputs. This is much more expensive than reading inputs from a mouse and keyboard or gamepad, as the headset must multiplex signals from accelerometers, cameras, and other sensors to determine where the user is in the world. This real-world position must then be mapped to a position in the simulated world.
5. **Scene Understanding**: Some WebXR experiences allow interactions between objects in the simulated world and objects in the real world. For example, a virtual tennis ball could bounce off your actual floor. Performing this simulation is expensive, as the device must estimate a 3D collision geometry for the floor, again processing large amounts of sensor data in real time.
6. **Device Constraints**: Most XR headsets run on mobile chipsets like the Qualcomm Snapdragon XR2, which are typically less powerful than those found in desktops, consoles, and laptops (though there is a wide variance in all of these devices). This means that an experience that runs at a stable 60 fps on a mid-range laptop may run at a much lower framerate on an XR headset. It is recommended to [throttle your CPU](https://developer.chrome.com/docs/devtools/settings/throttling/) in your web browser while profiling performance.

### Web Browser Performance Constraints

**WebAssembly Limitations**: [WebAssembly](https://developer.mozilla.org/en-US/docs/WebAssembly) (or Wasm) is a special binary instruction set that can be executed in all major browsers. Non-browser compatible languages like C++, Rust, and C# can be compiled to this binary format and then run in the browser, often leading to dramatic performance improvements when compared to JavaScript. Real-time 3D engines written in C++ or Rust like Unity and Bevy make use of Wasm to run games in the browser at "near-native speed" [0] . However, there are some limitations compared to native code:

- Startup Time: Like all web app source code, Wasm bytecode must be downloaded by the browser before it can begin running. This can result in worse startup time in comparison to native apps, where source is downloaded ahead of time.
- Garbage Collection: In Unity WebGL, garbage collection only runs at the end of each frame. This means that allocating many temporary values in a single frame can lead to "temporary quadratic memory growth pressure for the garbage collector" [1].
- Multi-threading: Threading support in Wasm is constantly evolving. Although the Wasm supports multi-threading and SIMD instructions, engines must explicitly support these Wasm features. For instance, Unity does not support C# multithreading [2].

**WebGL2 Limitations**: By default, rendering engines like Three.js, Babylon.js, Unity, and PlayCanvas use the [WebGL 2 API](https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API#webgl_2) to take advantage of hardware-accelerated graphics in the browser. WebGL 2 largely conforms to the Open GL ES 2.0 standard and dispatches commands to the GPU, meaning that the performance of a WebGL API call is very close to the corresponding native OpenGL call. However, there are some limitations to WebGL 2 vs. native code:

- Dispatching Overhead: There is overhead when dispatching WebGL calls on the CPU, as the WebGL API call must be translated into the correct native graphics API call [3]. Further, the browser implements more security checks than native code to prevent things like Out of Range Memory Accesses [4]. This slower dispatching limits the number of draw calls that can be performed in the browser.
- Because WebGL is based on OpenGL ES 2.0, it lacks many features that modern graphics APIs like DirectX 12, Vulkan, and Metal can provide [5], limiting the theoretical performance of an experience. The WebGPU browser API does support many of these features, but WebXR support is still being specified [6], and WebGPU support is experimental in most major engines [7], [8], [9].

**JavaScript Garbage Collection**

**Network Bandwidth**

## Why Build for the Web?

### Portability

### Security

### Adoption

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
