---
description: Resources for profiling 3D web applications and rendering engines
---

# Profiling and Testing 3D Web Experiences

***

## Table of Contents

[#profiling-web-applications-in-the-browser](profiling-and-testing-3d-web-experiences.md#profiling-web-applications-in-the-browser "mention")

[#profiling-resources-for-web-browsers-and-devices](profiling-and-testing-3d-web-experiences.md#profiling-resources-for-web-browsers-and-devices "mention")

[#overview-of-major-web-rendering-engines](profiling-and-testing-3d-web-experiences.md#overview-of-major-web-rendering-engines "mention")

***

## Profiling Web Applications in the Browser

In [the-principles-of-web-optimization.md](the-principles-of-web-optimization.md "mention"), we stressed the importance of profiling web applications before beginning any optimization. Here, we give an overview of how browser developer tools can be used to profile application metrics&#x20;

In this section, we use screenshots from Chrome, as it is has the largest user base. However, developer tooling functions similarly across browsers. For instance, Edge and Chrome use the exact same developer tools, with some minor UI differences. Safari has a slightly different collection of tools, but it can be used to achieve similar results. In the next section, we provide platform-specific resources for profiling web applications.

### :toolbox: Accessing Developer Tools

<figure><img src="../.gitbook/assets/Screenshot 2025-08-24 at 4.36.53 PM.png" alt=""><figcaption><p>Developer Tools in Chrome</p></figcaption></figure>

Most profiling utilities are accessible in the "Developer Tools" menu (Web Inspector in Safari). This menu is accessed in a different way on each browser:

| Browser | Command                                                  |
| ------- | -------------------------------------------------------- |
| Chrome  | <p>Ctrl-Shift-J (Windows)<br>Command-Option-K (Mac)</p>  |
| Edge    | F12                                                      |
| Safari  | Command-Option-C                                         |
| Firefox | <p>Ctrl-Shift-K. (Windows)<br>Command-Option-K (Mac)</p> |

### :stopwatch: Profiling Application Metrics

The Developer Tools browser menu can be used to access profilers for the core application metrics described in [the-principles-of-web-optimization.md](the-principles-of-web-optimization.md "mention"). Here, we give an overview of generating profiles with these tools and interpreting the results.

{% tabs %}
{% tab title="FPS" %}
#### Overview

We recommend enabling the built-in FPS overlay on desktop devices while developing an application. This allows developers to quickly detect if a change has impacted performance, even when not they are not doing optimization work. This helps prevent performance regressions from accidentally getting out to users.&#x20;

#### Guide: Enabling the FPS Overlay

Focus the Developer Tools Menu and enter `ctrl/cmd + shift + P` . This opens a search menu:&#x20;

<figure><img src="../.gitbook/assets/Screenshot 2025-08-24 at 4.39.24 PM.png" alt=""><figcaption></figcaption></figure>

Type "show FPS" into the search menu and hit `Enter`\
\
\
\


<figure><img src="../.gitbook/assets/Screenshot 2025-08-22 at 5.02.03 PM.png" alt=""><figcaption></figcaption></figure>

\
Observe the FPS counter in the top left corner of the application.

<figure><img src="../.gitbook/assets/Screenshot 2025-08-25 at 11.45.42 AM.png" alt=""><figcaption></figcaption></figure>

#### Interpreting Results

* Average FPS is displayed at the top of the overlay (69.6 FPS in the picture above)
* Variance in FPS can be determined from the colored bar below the average FPS. Yellow segments represent samples above 60 fps, while red segments represent samples below 60 fps. In the image above, FPS is hovering around 60 fps, so short red segments are interspersed with short yellow segments.&#x20;
*   In a well-optimized application, all segments will be yellow:&#x20;

    <figure><img src="../.gitbook/assets/Screenshot 2025-08-25 at 11.49.24 AM.png" alt=""><figcaption></figcaption></figure>
*   In a poorly-optimized application, many segments will be red:&#x20;

    <figure><img src="../.gitbook/assets/Screenshot 2025-08-25 at 11.52.14 AM.png" alt=""><figcaption></figcaption></figure>
* GPU memory usage is displayed at the bottom of the overlay. GPU performance typically scales with memory usage. In the image above, there is very little GPU overhead.&#x20;
{% endtab %}

{% tab title="General Performance" %}
#### Overview

Most optimization work in the browser revolves around generating profiles in the "Performance" tab (Timelines in Safari). In this tab, we can break down how much time is spent each frame on scripting, shaders, and garbage collection (GC). We can also view outlier frames, i.e. frames that last much longer than the average frame, contributing to instability.

#### Guide: Generating a Performance Profile

Select the Performance tab (Timelines in Safari) and click the "record" button.&#x20;

<figure><img src="../.gitbook/assets/Screenshot 2025-08-24 at 4.45.31 PM.png" alt=""><figcaption></figcaption></figure>

If your browser supports memory profiling, be sure to toggle the option. This allows you to analyze garbage collection pauses with more detail.

<figure><img src="../.gitbook/assets/Screenshot 2025-08-22 at 4.46.30 PM.png" alt=""><figcaption></figcaption></figure>

Developers can enable CPU throttling to approximate performance on less-powerful mobile devices. We recommend selecting "mid-tier mobile", as Chrome normalizes these values across devices.

<figure><img src="../.gitbook/assets/Screenshot 2025-08-25 at 12.00.47 PM.png" alt=""><figcaption></figcaption></figure>

The performance profiler generates a [flame graph](https://queue.acm.org/detail.cfm?id=2927301), which shows how much time is spent in individual functions in each frame. The functions stack downwards, so the core render function will be near the top, and native calls, JavaScript APIs, and WebGL calls will be at the bottom.

<figure><img src="../.gitbook/assets/Screenshot 2025-08-21 at 4.29.56 PM.png" alt=""><figcaption></figcaption></figure>

We also see the amount of memory being used during the profile. This typically has a "sawtooth" pattern, as garbage collection will automatically trigger once memory usage reaches a certain level.

<figure><img src="../.gitbook/assets/Screenshot 2025-08-25 at 12.21.51 PM.png" alt=""><figcaption></figcaption></figure>

Garbage collection appears in the flame graph as either "Minor GC" or "Major GC", representing a small or large amount of garbage collection overhead, respectively.

<figure><img src="../.gitbook/assets/Screenshot 2025-08-25 at 12.26.09 PM.png" alt=""><figcaption></figcaption></figure>

GPU overhead is recorded at the bottom of the profile, but there is relatively little detail provided.

<figure><img src="../.gitbook/assets/Screenshot 2025-08-25 at 12.39.03 PM.png" alt=""><figcaption></figcaption></figure>

#### Interpreting results

*   In a well-optimized application, the flamegraph will have a small width relative to the size of the length of the frame. In the screenshot below with mid-tier mobile throttling selected, we spend about 5ms per frame in scripting, leaving plenty of room for GPU overhead. This application can be expected to run at above 60 FPS on most devices:&#x20;

    <figure><img src="../.gitbook/assets/Screenshot 2025-08-25 at 11.57.39 AM.png" alt="" width="375"><figcaption></figcaption></figure>
* In a poorly-optimized application with the same throttling, we see much more time spent in scripting, over 20 ms in this case. This application will run well below 60 fps, as this does not include time spent on the GPU. Here, we can see a lot of time spent in the "render" function. This suggests that the developer should reduce the number of draw calls in the application.&#x20;

<figure><img src="../.gitbook/assets/Screenshot 2025-08-25 at 12.05.59 PM.png" alt="" width="323"><figcaption></figcaption></figure>

* In a well-optimized application, there should be a long time between Minor GC events. To achieve this, developers should reduce temporary allocations, as described in [#reusing-objects-to-reduce-memory-usage](the-principles-of-web-optimization.md#reusing-objects-to-reduce-memory-usage "mention").&#x20;
* A Major GC will correspond to a noticeable freeze in the application. These should be minimized as much as possible. Major garbage collection events can also be avoided by using temporary variables, but often correlate to a major scripting event, such as hundreds of meshes being removed from the scene at once. In these circumstances, it may be better to split up the work across multiple frames with [setTimeout](https://developer.mozilla.org/en-US/docs/Web/API/Window/setTimeout), allowing for multiple small GC pauses that are less noticeable to the user.
{% endtab %}

{% tab title="Memory Usage" %}
#### Overview

It can be challenging to locate the memory allocations that contribute to long garbage collection pauses or application crashes. In many cases, these allocations are split across many different functions and objects, each of which needs to be optimized. To identify these functions and objects, developers can take a "Heap Snapshot", which captures all of the application's allocated memory objects at a given time point and where they are still being used.

#### Generating a Memory Snapshot

Select the Memory tab in Developer Tools

<figure><img src="../.gitbook/assets/Screenshot 2025-08-22 at 4.47.47 PM.png" alt=""><figcaption></figcaption></figure>

Click the record button to take a heap snapshot

<figure><img src="../.gitbook/assets/Screenshot 2025-08-24 at 4.41.54 PM.png" alt=""><figcaption></figcaption></figure>

This generates an overview of how much memory is consumed by your application, and where the majority of memory is allocated. It is important to minimize the amount of temporary "object" and "array" memory used by your application, as this memory contributes the most to garbage collection pauses.

<figure><img src="../.gitbook/assets/Screenshot 2025-08-22 at 4.47.20 PM.png" alt=""><figcaption></figcaption></figure>

#### Interpreting Results

**Minimizing large objects**

Select an object that is utilizing lots of memory. Typically, these will be ArrayBuffers, Meshes, Vectors, other game engine objects, or plain JavaScript arrays like the ArrayBuffer below:

<figure><img src="../.gitbook/assets/Screenshot 2025-08-25 at 12.54.58 PM.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2025-08-25 at 12.46.27 PM.png" alt=""><figcaption></figcaption></figure>

We can then see that the ArrayBuffer is being allocated as matrix transform data for instances of a mesh. Given the large amount of memory being allocated for this matrix, it may make sense to reduce the number of instances rendered by the corresponding mesh, as this could cause a GC pause when it is disposed.

<figure><img src="../.gitbook/assets/Screenshot 2025-08-25 at 12.52.37 PM.png" alt=""><figcaption></figcaption></figure>

**Minimizing large quantities of small objects**

In addition to the large ArrayBuffer allocation, we also see a large number of Vector3 objects being allocated (43,363)

<figure><img src="../.gitbook/assets/Screenshot 2025-08-25 at 1.01.44 PM.png" alt=""><figcaption></figcaption></figure>

Most of these Vector3 objects are in use by the engine, but some of them correspond to temporary variables created in a loop in our experience logic. These temporary variables should ideally be reused, as described in [#reusing-objects-to-reduce-memory-usage](the-principles-of-web-optimization.md#reusing-objects-to-reduce-memory-usage "mention").
{% endtab %}

{% tab title="Loading Time" %}
#### Overview

There are three main tools for profiling loading time: the performance profiler, Lighthouse (only available in Chrome and Edge), and the network tab. Lighthouse generates a report on how long the application takes to load

#### Guide: Profiling Loading Performance

To profile scripting performance while loading, reload the page while recording performance. This generates a performance profile that is identical to the one described in [#general-performance](profiling-and-testing-3d-web-experiences.md#general-performance "mention").

<figure><img src="../.gitbook/assets/Screenshot 2025-08-22 at 4.49.15 PM.png" alt=""><figcaption></figcaption></figure>

In the generated profile, we can see lots of time being spent decoding textures. This could be reduced by using smaller texture sizes, which take less time to decode, or by merging textures into a single texture atlas.

<figure><img src="../.gitbook/assets/Screenshot 2025-08-25 at 1.20.04 PM.png" alt="" width="353"><figcaption></figcaption></figure>

#### Guide: Generating Lighthouse Reports

Chrome has built-in tools for analyzing loading time performance in the "Lighthouse" tab.&#x20;

<figure><img src="../.gitbook/assets/Screenshot 2025-08-22 at 4.48.26 PM.png" alt=""><figcaption></figcaption></figure>

This gives information on asset download sizes, how long the initial load takes, and how long it takes for the application to come fully interactive. This is useful in determining TTID. TTID roughly corresponds to the "First Contentful Paint" metric. There is not an exact analogue for TTFD, as Lighthouse cannot determine whether or not the canvas is interactive.

<figure><img src="../.gitbook/assets/Screenshot 2025-08-24 at 4.50.16 PM.png" alt=""><figcaption></figcaption></figure>

In a poorly optimized application, Lighthouse will give information on specific assets to optimize, such as textures, meshes, and code.

<figure><img src="../.gitbook/assets/Screenshot 2025-08-25 at 1.14.09 PM.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2025-08-25 at 1.13.57 PM.png" alt=""><figcaption></figcaption></figure>

#### Guide: Inspecting Network Downloads

The network tab can be used to inspect the size and timing of network fetches. To profile network activity, open the network tab, and reload the page. Select "Disable Cache" to ensure that everything is downloaded over the network.

<figure><img src="../.gitbook/assets/Screenshot 2025-08-25 at 1.18.52 PM.png" alt=""><figcaption></figcaption></figure>

Look for assets with large sizes. Here, we may choose to compress the skybox texture even more, as the combined assets are about 6 MB.

<figure><img src="../.gitbook/assets/Screenshot 2025-08-25 at 1.16.46 PM.png" alt=""><figcaption></figcaption></figure>
{% endtab %}
{% endtabs %}

### :mobile\_phone: Profiling Mobile Devices

There are two main ways to capture mobile performance metrics:&#x20;

* directly profiling a connected mobile device
* emulating a mobile device in the browser

These techniques should be used in combination. Profiling mobile hardware gives more representative data, while emulation can be used to provide a wider range of data points. Crucially, GPU performance does not scale in browser emulators, so frame rate may be higher in the emulator. Emulators should primarily be used to approximate CPU performance across a range of devices and to validate image quality across a range of resolutions.

{% tabs %}
{% tab title="Android" %}
#### Prerequisites

1. Access to an Android Device
2. Install Android Debug Bridge ([ADB](https://developer.android.com/tools/adb)) to connect to the android device.
3. Install Chrome or Edge on your desktop/laptop computer. In Edge, replace `chrome://` with `edge://` for the following steps.

#### Connecting to an Android Device via USB

1. Connect an android device via USB
2. In a terminal, run `adb devices` to start the debugging server
3. Navigate to chrome://inspect/devices and click "inspect" the connected device
4. This brings up the Developer Tools menu for the connected device. From here, profiling is the same as on a desktop

#### Generating Native Performance Profiles

1. In chrome://inspect/devices, select the "trace" option.
2. If debugging an Android XR device, select `Edit categories` > check `xr.debug`
3. Click record to begin generating a native trace.
{% endtab %}

{% tab title="iOS" %}
#### Prerequisites

1. Access to an iOS device
2. [Enable inpsecting safari on the iOS Device](https://developer.apple.com/documentation/safari-developer-tools/inspecting-ios#Enabling-inspecting-your-device-from-a-connected-Mac)

#### Connecting to an iOS device

1. [Connect to the device over WiFi or USB](https://developer.apple.com/documentation/safari-developer-tools/inspecting-ios#Enabling-inspecting-your-device-from-a-connected-Mac)
2. [Open Safari > Develop > Your Device > Your Application](https://developer.apple.com/documentation/safari-developer-tools/inspecting-ios#Inspecting-a-webpage)
3. Profile the application using safari dev tools.
{% endtab %}

{% tab title="Mobile Device Emulation" %}
#### Emulating a Mobile Device

Open Developer Tools and select the icon in the top left corner that looks like a laptop computer. This enables device emulation, allowing a developer to select from a range of devices to emulate.

<figure><img src="../.gitbook/assets/Screenshot 2025-08-25 at 1.22.43 PM.png" alt=""><figcaption></figcaption></figure>

Developers can then select a device to emulate in the main browser window, and apply throttling to the CPU. Below, we selected an iPhone 12 Pro with Mid-tier Mobile throttling:

<figure><img src="../.gitbook/assets/Screenshot 2025-08-25 at 1.27.03 PM.png" alt=""><figcaption></figcaption></figure>
{% endtab %}

{% tab title="WebXR Emulation" %}
#### Emulating a WebXR Device

For WebXR Emulation, install the [Meta Immersive Web Emulator](https://developers.meta.com/horizon/blog/webxr-development-immersive-web-emulator/). It is supported on Chrome and Edge. This allows developers to profile immersive experiences more accurately, and gives tools for manipulating the XR headset and controllers:\


<figure><img src="../.gitbook/assets/Screenshot 2025-08-25 at 1.29.01 PM (2).png" alt=""><figcaption></figcaption></figure>
{% endtab %}
{% endtabs %}

***

## Profiling Resources for Web Browsers and Devices

Beyond the overview we have provided, each browser provides detailed guides for debugging and profiling applications. Here we provide lists of resources for each major browser, as well as resources for connecting to mobile devices from Chrome and Safari.

{% tabs %}
{% tab title="Chrome" %}
#### Platform Support

:desktop: Desktop & :mobile\_phone: Mobile

#### Core Developer Tools

* [Performance Profiling](https://developer.chrome.com/docs/devtools/performance/overview)
* [Debugger](https://developer.chrome.com/docs/devtools/javascript)
* [Network Profiling](https://developer.chrome.com/docs/devtools/network/overview)
* [Memory Profiling](https://developer.chrome.com/docs/devtools/memory)

#### Additional Tooling

* [Tracing Tool](https://www.chromium.org/developers/how-tos/trace-event-profiling-tool/): Provides more introspection into the native side of chrome, showing JavaScript engine traces and GPU tracing.
* [Lighthouse](https://developer.chrome.com/docs/devtools/lighthouse): Tools for optimizing app start time and file size.
* [Immersive Web Emulator Plugin](https://chromewebstore.google.com/detail/immersive-web-emulator/cgffilbpcibhmcfbgggfhfolhkfbhmik?pli=1): emulate XR devices in Chrome Developer Tools
* Display [frame rate](https://devtoolstips.org/tips/en/display-current-framerate/)
* [WebAssembly debugger](https://developer.chrome.com/docs/devtools/wasm)

#### Mobile Tooling

* [Remote Debugging](https://developer.chrome.com/docs/devtools/remote-debugging/local-server)
* [Device Emulator](https://developer.chrome.com/docs/devtools/device-mode/)
{% endtab %}

{% tab title="Edge" %}
#### Platform Support

&#x20;:desktop: Desktop

#### Core Developer Tools

* [Performance Profiling](https://learn.microsoft.com/en-us/microsoft-edge/devtools/performance/overview)
* [Debugger](https://learn.microsoft.com/en-us/microsoft-edge/devtools/javascript/)
* [Network Profiling](https://learn.microsoft.com/en-us/microsoft-edge/devtools/network/)
* [Memory Profiling](https://learn.microsoft.com/en-us/microsoft-edge/devtools/memory-problems/)

#### Additional Tooling

* [Immersive Web Emulator Plugin](https://microsoftedge.microsoft.com/addons/detail/immersive-web-emulator/hhlkbhldhffpeibcfggfndbkfohndamj): emulate XR devices in Edge Developer Tools

#### Mobile Tooling

* [Device Emulator](https://learn.microsoft.com/en-us/microsoft-edge/devtools/device-mode/)
{% endtab %}

{% tab title="Safari" %}
#### Platform Support

Desktop & :mobile\_phone: Mobile

#### Core Developer Tools

* [Tools Overview](https://developer.apple.com/documentation/safari-developer-tools/web-inspector)
* [Timelines](https://webkit.org/web-inspector/timelines-tab/) (Scripting and Memory profiler)
* [Debugger](https://webkit.org/web-inspector/sources-tab/)
* [Network Profiler](https://webkit.org/web-inspector/network-tab/)

#### Mobile Tooling

* [Inspecting iOS devices](https://developer.apple.com/documentation/safari-developer-tools/inspecting-ios)
{% endtab %}
{% endtabs %}

***

## Overview of Major Web Rendering Engines

In this section, we discuss the features, strengths, and weaknesses of popular 3D rendering engines for the web. Each of these engines is capable of creating high-quality 3D experiences, but developers may prefer one engine over another based on the type of experience they are developing and their development background.

### <img src="../.gitbook/assets/UnityLogo.png" alt="" data-size="line"> Unity

Unity is a feature-packed native game engine that has been used to make some of the most popular indie games of all time. If you have experience developing with other native engines like Unreal Engine and Godot, Unity's development flow will feel natural to you. However, optimizing file size is a challenge for Unity experiences, as Unity supports both native and web platforms.

#### Overview

{% columns %}
{% column width="50%" %}
#### :star: Engine Features

2D, 3D, Particles, Photorealistic Rendering, Animations, User Interface, Physics

#### :mobile\_phone:Platform Compatibility

* Built-in support for mobile touch controls
* Integrates well with WebXR

#### :earth\_africa: Developer Community

* Huge community, though the web-specific community is relatively small
* Extensive documentation
* Numerous third-party plugins, though native plug-ins must be manually compiled to Wasm
{% endcolumn %}

{% column width="50%" %}
#### :technologist: Developer Experience

* First-Class 3D Editor
* Built-In Version Control
* Extensive tooling for profiling experiences
* Integrates with browser Developer Tools

#### :scroll: Licensing

* Closed source engine
* Free for hobbyists and indie teams
* Paid for teams with over $200k annual revenue.
{% endcolumn %}
{% endcolumns %}

#### Strengths and Weaknesses

| Strengths                                                                                                                                              | Neutrals                                                                                              | Weaknesses                                                                                                |
| ------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| <ul><li>High performance ceiling</li><li>First-class feature set</li><li>First-class editor</li><li>Experiences scale well on mobile devices</li></ul> | <ul><li>Native game engine</li><li>Write in C#</li><li>Opinionated game engine architecture</li></ul> | <ul><li>WebGL rendering is not the focus of the engine</li><li>Produces large builds by default</li></ul> |

#### Resources

<table><thead><tr><th valign="top">Documentation</th><th valign="top">Optimization Guides</th><th valign="top">Profiling Tools and Guides</th></tr></thead><tbody><tr><td valign="top"><ul><li><a href="https://docs.unity3d.com/Manual/index.html">Core Documentation</a></li></ul><ul><li><a href="https://docs.unity3d.com/Manual/webgl.html">WebGL Documentation</a></li></ul><ul><li><a href="https://docs.unity3d.com/Manual/webgl-gettingstarted.html">Publishing Guide</a></li></ul><ul><li><a href="https://docs.unity3d.com/Manual/webgl-building-distribution.html">Creating Unity Web Builds</a></li></ul></td><td valign="top"><p>General Performance</p><ul><li><a href="https://docs.unity3d.com/Manual/analysis.html">General Unity Optimization</a></li><li><a href="https://docs.unity3d.com/Manual/optimizing-draw-calls.html">Optimizing Draw Calls</a></li><li><a href="https://docs.unity3d.com/Manual/SL-ShaderPerformance.html">Optimizing Shaders</a></li></ul><ul><li><a href="https://docs.unity3d.com/Manual/webgl-technical-overview.html">Technical Limitations</a></li></ul><ul><li><a href="https://docs.unity3d.com/Manual/webgl-memory.html">Web Memory Optimization</a></li></ul><ul><li><a href="https://docs.unity3d.com/Manual/web-graphics-apis-intro.html">Web Graphics Recommendations</a></li></ul><ul><li><a href="https://docs.unity3d.com/Manual/webgl-texture-compression.html">Texture Compression</a></li></ul><ul><li><a href="https://docs.unity3d.com/Manual/wasm-2023-features.html">WebAssembly Optimization</a></li></ul><p>Loading Time</p><ul><li><a href="https://docs.unity3d.com/Manual/web-optimization.html">Optimizing Web Builds</a></li></ul><ul><li><a href="https://docs.unity3d.com/Manual/web-optimization-mobile.html">Optimizing Web Builds for Mobile</a></li></ul></td><td valign="top"><ul><li><a href="https://docs.unity3d.com/Manual/Profiler.html">Unity Profiler</a></li></ul><ul><li><a href="https://docs.unity3d.com/Manual/performance-profiling-tools.html">Other Profiling Tools</a></li></ul><ul><li><a href="https://docs.unity3d.com/Manual/graphics-performance-profiling.html">Graphics Performance Profiling</a></li></ul><ul><li><a href="https://docs.unity3d.com/6000.3/Documentation/Manual/webgl-debugging.html">Debug Web Builds</a></li></ul><ul><li><a href="https://unity.com/how-to/profile-optimize-web-build#the-importance-of-profiling">How to profile Web Builds</a></li></ul></td></tr></tbody></table>

### <img src="../.gitbook/assets/ThreeJSLogo.png" alt="" data-size="line"> Three.js

Three.js is the most popular 3D rendering library written in JavaScript. It is powerful, lightweight, and highly extensible, making it a good choice for developers who want to keep build sizes small and have more control over the rendering performance of their experience. However, it has few features by default, and must be extended with open source libraries to implement physics and user interfaces.

#### Overview

{% columns %}
{% column width="50%" %}
#### :star: Engine Features

3D, Photorealistic Rendering, Animations

#### :mobile\_phone:Platform Compatibility

* No built-in support for mobile touch controls
* Integrates well with WebXR

#### :earth\_africa: Developer Community

* Large community
* Good documentation, numerous examples and tutorials available
* Huge selection of third-party libraries that enable features like physics and user interfaces
* Frameworks like [A-frame](https://aframe.io/) turn Three.js into a true game engine
* Integrates very well with React
{% endcolumn %}

{% column width="50%" %}
#### :technologist: Developer Experience

* Extremely rudimentary 3D editor
* No built-in version control
* Integrates well with browser Developer Tools

#### :scroll: Licensing

* Open source engine
* Free to use
{% endcolumn %}
{% endcolumns %}

#### Strengths and Weaknesses

| Strengths                                                   | Neutrals                                                                                                                      | Weaknesses                                                                                                   |
| ----------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| <ul><li>Very small builds</li><li>Large community</li></ul> | <ul><li>JavaScript engine</li><li>Flexible architecture</li><li>Requires lots of expertise to get great performance</li></ul> | <ul><li>Only provides rendering logic. Features like UI and physics require third-party libraries </li></ul> |

#### Resources

<table><thead><tr><th valign="top">Documentation</th><th valign="top">Optimization Guides</th><th valign="top">Profiling Tools and Guides</th></tr></thead><tbody><tr><td valign="top"><ul><li><a href="https://threejs.org/manual/#en/creating-a-scene">Documentation</a></li></ul><ul><li><a href="https://discourse.threejs.org/">Forum</a></li><li><a href="https://threejsresources.com/">Learning Resources</a></li></ul></td><td valign="top"><p>General Performance</p><ul><li><a href="https://threejs.org/manual/#en/optimize-lots-of-objects">Rendering Many Objects</a></li></ul><ul><li><a href="https://threejs.org/manual/#en/optimize-lots-of-objects-animated">Rendering Many Animated Objects</a></li><li><a href="https://threejs.org/manual/#en/offscreencanvas">Leveraging Offscreen Canvas</a></li><li><a href="https://threejs.org/manual/#en/cleanup">Memory Management</a></li><li><a href="https://threejs.org/docs/?q=inst#api/en/objects/InstancedMesh">InstancedMesh</a></li><li><a href="https://tympanus.net/codrops/2025/02/11/building-efficient-three-js-scenes-optimize-performance-while-maintaining-quality/">Building Efficient Three.js Scenes</a></li></ul></td><td valign="top"><ul><li><a href="https://threejs.org/manual/#en/debugging-javascript">JavaScript Debugging</a></li></ul><ul><li><a href="https://chromewebstore.google.com/detail/threejs-devtools/jechbjkglifdaldbdbigibihfaclnkbo">DevTools Plugin</a></li><li><a href="https://threejs.org/manual/#en/debugging-glsl">GLSL Debugging</a></li><li><a href="https://github.com/mrdoob/stats.js/">Stats.js</a>: JavaScript Performance Monitor</li></ul></td></tr></tbody></table>

### <img src="../.gitbook/assets/PlayCanvasLogo.png" alt="" data-size="line"> PlayCanvas

PlayCanvas is a fully-featured JavaScript engine that has a great developer experience and enables high performance web applications. The engine is less flexible than other JavaScript engines, using a more opinionated Entity-Component System architecture to ensure consistent performance.

#### Overview

{% columns %}
{% column width="50%" %}
#### :star: Engine Features

3D, Particles, Animations, Photorealistic Rendering, Physics, User Interface

#### :mobile\_phone:Platform Compatibility

* Built-in support for mobile touch controls
* Integrates with WebXR

#### :earth\_africa: Developer Community

* Medium-size community
* Extensive documentation
* Integrates well with React
* Relatively few third-party libraries
{% endcolumn %}

{% column width="50%" %}
#### :technologist: Developer Experience

* First-Class 3D Editor
* Built-In Version Control
* Extensive tooling for profiling experiences
* Integrates with browser Developer Tools

#### :scroll: Licensing

* Open source engine
* Free to use source code
* Some editor features are available as a paid subscription
{% endcolumn %}
{% endcolumns %}

#### Strengths and Weaknesses

| Strengths                                                                                        | Neutrals                                                                 | Weaknesses                                      |
| ------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------ | ----------------------------------------------- |
| <ul><li>High performance ceiling</li><li>Feature-rich editor</li><li>Small build sizes</li></ul> | <ul><li>JavaScript engine</li><li>Opinionated ECS Architecture</li></ul> | <ul><li>Some editor features are paid</li></ul> |

#### Resources

<table><thead><tr><th valign="top">Documentation</th><th valign="top">Optimization Guides</th><th valign="top">Profiling Tools and Guides</th></tr></thead><tbody><tr><td valign="top"><ul><li><a href="https://developer.playcanvas.com/">Documentation</a></li></ul><ul><li><a href="https://developer.playcanvas.com/tutorials/">Tutorials</a></li><li><a href="https://playcanvas.com/products/editor">Editor</a></li><li><a href="https://forum.playcanvas.com/">Forums</a></li></ul></td><td valign="top"><p>General Performance</p><ul><li><a href="https://developer.playcanvas.com/user-manual/optimization/">Optimization</a></li><li><a href="https://developer.playcanvas.com/user-manual/optimization/texture-compression/">Texture Compression</a></li><li><a href="https://developer.playcanvas.com/user-manual/graphics/advanced-rendering/batching/">Batching</a></li><li><a href="https://developer.playcanvas.com/user-manual/graphics/advanced-rendering/hardware-instancing/">Hardware Instancing</a></li><li><a href="https://developer.playcanvas.com/user-manual/graphics/advanced-rendering/indirect-drawing/">Indirect Drawing</a></li></ul><ul><li><a href="https://developer.playcanvas.com/user-manual/xr/optimizing-webxr/">Optimizing WebXR</a></li></ul><p>Loading Time</p><ul><li><a href="https://developer.playcanvas.com/user-manual/optimization/load-time/">Optimizing Load Time</a></li></ul></td><td valign="top"><ul><li><a href="https://developer.playcanvas.com/user-manual/scripting/debugging/browser-dev-tools/">Browser Dev Tools</a></li></ul><ul><li><a href="https://developer.playcanvas.com/user-manual/optimization/gpu-profiling/">GPU Profiling</a></li><li><a href="https://threejs.org/manual/#en/debugging-javascript">JavaScript Debugging</a></li></ul><p></p></td></tr></tbody></table>

### <img src="../.gitbook/assets/BabylonJSLogo.png" alt="" data-size="line">Babylon.js

Babylon.js is a fully-featured JavaScript engine that enables great performance, is easy to learn, and has a solid developer tooling ecosystem. Project sizes tend to be larger than PlayCanvas or Three.js, but smaller than Unity. For developers that do not need an editor but want all the features of a modern web rendering engine, Babylon is a great choice.

#### Overview

{% columns %}
{% column width="50%" %}
#### :star: Engine Features

3D, Particles, Animations, Photorealistic Rendering, Physics, User Interface

#### :mobile\_phone:Platform Compatibility

* No built-in touch controls
* Integrates well with WebXR

#### :earth\_africa: Developer Community

* Small, active community
* Extensive documentation
* Integrates with JavaScript libraries like React, Angular, and Vue
{% endcolumn %}

{% column width="50%" %}
#### :technologist: Developer Experience

* Rudimentary 3D editor
* Rudimentary playground system for prototyping in the browser
* Node-based material editor
* No built-in version control
* The Babylon Inspector and Spector libraries enable extensive profiling in the browser
* Integrates with browser Developer Tools

#### :scroll: Licensing

* Open source engine
* Free to use
{% endcolumn %}
{% endcolumns %}

#### Strengths and Weaknesses

| Strengths                                                                                                   | Neutrals                                                                                         | Weaknesses                                                                                     |
| ----------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------- |
| <ul><li>High performance ceiling</li></ul><ul><li>First class WebXR</li><li>Extensive feature set</li></ul> | <ul><li>JavaScript engine</li><li>Flexible architecture</li><li>Supported by Microsoft</li></ul> | <ul><li>Larger file sizes for a JavaScript Engine</li><li>Relatively small community</li></ul> |

#### Resources

<table><thead><tr><th valign="top">Documentation</th><th valign="top">Optimization Guides</th><th valign="top">Profiling Tools and Guides</th></tr></thead><tbody><tr><td valign="top"><ul><li><a href="https://doc.babylonjs.com/">Documentation</a></li></ul><ul><li><a href="https://playground.babylonjs.com/">Playground</a></li><li><a href="https://forum.babylonjs.com/">Forums</a></li></ul></td><td valign="top"><p>General Performance</p><ul><li><a href="https://doc.babylonjs.com/features/featuresDeepDive/scene/optimize_your_scene">Optimization Guide</a></li></ul><ul><li><a href="https://doc.babylonjs.com/features/featuresDeepDive/scene/optimizeOctrees/">Octree Optimization</a></li></ul><ul><li><a href="https://doc.babylonjs.com/features/featuresDeepDive/scene/sceneOptimizer/">SceneOptimizer</a></li><li><a href="https://doc.babylonjs.com/features/featuresDeepDive/scene/offscreenCanvas/">Offscreen Canvas</a></li><li><a href="https://doc.babylonjs.com/features/featuresDeepDive/scene/customLoadingScreen/">Creating Loading Screens</a></li><li><a href="https://doc.babylonjs.com/features/featuresDeepDive/mesh/copies/">Instancing</a></li></ul><p>Loading Time</p><ul><li><a href="https://doc.babylonjs.com/setup/frameworkPackages/es6Support/">Optimizing build sizes</a></li><li><a href="https://doc.babylonjs.com/features/featuresDeepDive/scene/optimizeCached/">Resource Caching</a></li></ul></td><td valign="top"><ul><li><a href="https://github.com/BabylonJS/Spector.js">Spector</a>: a tool for analyzing draw calls</li></ul><ul><li><a href="https://doc.babylonjs.com/toolsAndResources/inspector/">Babylon Inspector</a></li></ul></td></tr></tbody></table>
