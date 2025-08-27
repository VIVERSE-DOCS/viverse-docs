---
description: >-
  An overview of why developers should build for the web, how optimizing
  experiences for the web is different from native, and what characteristics
  define an optimized experience.
---

# Introduction to Optimizing for the Web

***

## Why Build for the Web?

<h3 align="center"><span data-gb-custom-inline data-tag="emoji" data-code="1f30d">🌍</span> Reach millions of users instantly <span data-gb-custom-inline data-tag="emoji" data-code="1f30d">🌍</span></h3>

<p align="center">An estimated <strong>3 billion people</strong> have access to a 3D-capable web browser and high-speed internet connection<a href="https://datareportal.com/reports/digital-2025-global-overview-report"><sup>0</sup></a><sup>,</sup><a href="https://ngital.com/bangladesh-internet-penetration-2025-data-insights/"><sup>1</sup></a><sup>,</sup><a href="https://africa.businessinsider.com/local/lifestyle/african-countries-with-the-largest-internet-population-in-2025/871gpnf"><sup>2</sup></a><sup>,</sup><a href="https://www.itu.int/itu-d/reports/statistics/2024/11/10/ff24-internet-use/"><sup>3</sup></a><sup>,</sup><a href="https://datareportal.com/reports/digital-2025-sub-section-accelerated-access"><sup>4</sup></a>. Millions of users play video games in the browser each month, providing a wide reach for your 3D experience.</p>

<figure><img src="../.gitbook/assets/3D Capable Demographics (1).png" alt="" width="375"><figcaption></figcaption></figure>

<h3 align="center"><span data-gb-custom-inline data-tag="emoji" data-code="1f680">🚀</span> Launch on any device with a 3D-compatible web browser <span data-gb-custom-inline data-tag="emoji" data-code="1f680">🚀</span></h3>

<p align="center">3D web apps reach phones, desktop computers, laptop computers, and mixed reality (XR) headsets via the same URL. The vast majority of internet activity happens in 3D-capable browsers (Chrome, Edge, Firefox, and Safari)<a href="https://gs.statcounter.com/browser-market-share"><sup>5</sup></a> running on 3D-capable operating systems (Android, iOS, OSX, Windows)<a href="https://www.gsma.com/r/wp-content/uploads/2024/10/The-State-of-Mobile-Internet-Connectivity-Report-2024.pdf"><sup>6</sup></a>.</p>

{% columns %}
{% column %}
<figure><img src="../.gitbook/assets/Browser Demographics (1) (1).png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}

{% column %}
<figure><img src="../.gitbook/assets/OS Demographics (1).png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

<p align="center">An estimated 99% of browsers in use support 3D experiences via the WebGL API<a href="https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API#api.webgl2renderingcontext"><sup>7</sup></a>.</p>

<figure><img src="../.gitbook/assets/WebGL Support (1).png" alt="" width="375"><figcaption></figcaption></figure>

<h3 align="center"><span data-gb-custom-inline data-tag="emoji" data-code="1f510">🔐</span> Secure by default <span data-gb-custom-inline data-tag="emoji" data-code="1f510">🔐</span></h3>

<p align="center">The <a href="https://developer.mozilla.org/en-US/docs/Web/Security">web’s</a> application sandbox, HTTPS, and permissions model let you consume powerful graphics, XR, and platform features with user consent and origin isolation.</p>

<h3 align="center"><span data-gb-custom-inline data-tag="emoji" data-code="1f91d">🤝</span> The same standards for everyone <span data-gb-custom-inline data-tag="emoji" data-code="1f91d">🤝</span></h3>

<p align="center">APIs and technologies like <a href="https://registry.khronos.org/webgl/specs/latest/2.0/">WebGL</a>, <a href="https://www.w3.org/TR/webgpu/">WebGPU</a>, <a href="https://www.w3.org/TR/webxr/">WebXR</a>, <a href="https://www.w3.org/groups/wg/wasm/">WebAssembly</a> (WASM), <a href="https://www.w3.org/Style/CSS/">CSS</a>, and JavaScript are stewarded by mature standards bodies and implemented across major web browsers. There is no store gatekeeping compared to native platforms.</p>

<h3 align="center"><span data-gb-custom-inline data-tag="emoji" data-code="1fab6">🪶</span> No download required <span data-gb-custom-inline data-tag="emoji" data-code="1fab6">🪶</span></h3>

<p align="center">Web experiences do not require users to download an application ahead of time from a store, making it simple for new users to try out your experience and share it with friends.</p>

***

## What makes an experience well-optimized?

{% hint style="info" %}
Developing for the web comes with many advantages, but developers must ensure that the experience performs well on many devices. What's more, there is a large variance of 3D performance within device classes; a mobile user could be on the latest iPhone or an older budget device. While developers instinctively know what an optimized experience feels like, it can be hard to describe. Here, we outline six characteristics that describe a well-optimized experience.
{% endhint %}

{% columns fullWidth="false" %}
{% column %}
### :zap: Fast-loading

Research shows that users are much more likely to return to applications that load quickly. In a well-optimized experience, users can expect to see a loading indicator in milliseconds and interact with the scene in seconds.

### :rock: Stable

A stable scene produces new images at a consistent rate, regardless of frequency. When there are random gaps between new images, the user's immersion is broken, just like when a video buffers.

### :saluting\_face: Responsive

In a responsive experience, the scene updates immediately in response to user inputs. Users expect the camera to track directly with inputs, and can even experience nausea if there is too much latency. Beyond the camera movement, users expect UI elements to respond to clicks and player animations to trigger on button presses. It is easy to break immersion when an experience is not responsive.
{% endcolumn %}

{% column %}
### :ocean:  Fluid

Fluidity, or smoothness, is the characteristic that users typically associate with performance. Fluidity is defined by how a scene looks in motion; in a fluid scene, the user should not be able to perceive the gaps in time between each image, instead perceiving a continuous stream of imagery.

### :mag: Legible

A scene may be smooth and stable, but this does not matter if individual elements in the scene cannot be interpreted. Users must be able to read all UI elements and identify models in motion. This characteristic is most related to screen and texture resolution. High [aliasing](https://en.wikipedia.org/wiki/Aliasing), or blurriness, also reduces legibility.

### :triangular\_ruler: Scalable

Regardless of whether an experience runs in a mobile browser or desktop browser, users expect performance comparable to native applications on that platform. A scalable experience tunes its performance to the device it's running on.
{% endcolumn %}
{% endcolumns %}

***

## Optimizing 3D experiences for the web vs native platforms

While the core principles of game development are universal, building on the web requires additional optimization to ensure that users have a great first impression and keep coming back.

### :bullettrain\_front: Reduce loading times

{% hint style="info" %}
**Users expect web experiences to load more quickly than native experiences.**&#x20;

Native applications are downloaded ahead of launch, making it much easier to achieve fast loading times. Web applications must achieve better launch times while downloading assets after launch.
{% endhint %}

* **Keep assets small** - users have to download assets over their network on each page load. Keeping assets small makes your experience accessible to users with slower internet speeds and speeds up the loading time. The average user has an internet speed of 50 megabits per second (mbps) and each extra second of waiting time increases [bounce rates](https://en.wikipedia.org/wiki/Bounce_rate) by [14%](https://www.tooltester.com/en/blog/website-loading-time-statistics/).
* **Load in assets as you go** - one benefit of the browser is that assets can be continuously fetched over the network. Rather than loading everything in at once, only download it when you need it.
* **Show the user something as soon as you can** - users will be visiting your game from a web page, which typically load very quickly. To keep the experience seamless, make sure to render a loading bar while resources are downloading.

### :desktop: :computer: Prepare for cross-platform use :mobile\_phone::goggles:

{% hint style="info" %}
**The same build runs across mobile, desktop, and XR platforms**

Unlike native applications, where a separate build must be created for each platform, only one build is generated for the web. This simplifies deployment, but it makes it harder to ensure that apps perform well on all platforms. Developers should update performance settings while the app is running to ensure the best experience.
{% endhint %}

* **Set performance settings on launch** - web users expect that the default graphics settings will perform well on launch. Use lower graphics settings on mobile and XR than on desktop.
* **Scale performance as you go** - track performance metrics while the app is running, and scale graphics settings up or down accordingly.
* **Determine the correct input method** - make sure that the correct input format is selected for each platform you support: touch inputs for mobile; keyboard/mouse on desktop; and controllers on XR. Be prepared to detect gamepads and alternate input sources on each platform.
* **Scale resolution according to the device** - users expect UI elements and resolution to scale with the size of the screen. Mobile devices typically run at a lower resolution with larger UI elements, while desktops run at a higher resolution with proportionally smaller UI elements. Mobile devices also support both landscape and portrait mode; ensure that the experience renders well in portrait mode or notify the user that the game must run in landscape mode.

### :radio: Optimize scenes for the browser

{% hint style="info" %}
**Developers must tailor their experience towards running in a WebGL context**

Running applications in the browser adds some overhead for extra security. Developers should take this extra overhead into account when designing their scene.
{% endhint %}

* **Account for WebGL execution overhead** - the browser adds security measures to ensure that APIs like [WebGL](https://registry.khronos.org/webgl/specs/1.0/#ATTRIBS_AND_RANGE_CHECKING) can't be used to run malicious code. This adds some CPU overhead to 3D web applications compared to native applications, reducing the total number of WebGL commands we can execute in a frame. However, once the CPU passes the command to the GPU, rendering performance is largely the same [compared to an OpenGL application](https://docs.unity3d.com/6000.2/Documentation/Manual/webgl-performance.html).
* **Understand how** [**draw calls**](https://howik.com/understanding-draw-calls) **impact performance, and** [**how to reduce them**](the-principles-of-web-optimization.md#reducing-and-batching-draw-calls) - CPU performance scales with the number of draw calls that occur in each frame. A draw call is a command that the engine sends to the GPU telling it to a draw a series of triangles or pixels. This operation happens quickly on the GPU, but is slow on the CPU, making it advantageous to batch draw calls.
* **Reduce scene complexity** - You may need to reduce mesh counts, render animations at lower frame rates, and/or remove transparency and reflections.

### :fire\_engine: Select the best engine for the experience

{% hint style="info" %}
**Different web engines are well-suited for different types of experiences**

There are numerous feature-packed 3D rendering engines for the web, like [PlayCanvas](https://playcanvas.com/), [Unity](https://docs.unity3d.com/6000.2/Documentation/Manual/webgl.html), [Three.js](https://threejs.org/), and [Babylon.js](https://www.babylonjs.com/). Each engine has different strengths and weaknesses, explored more in [profiling-and-testing-3d-web-experiences.md](profiling-and-testing-3d-web-experiences.md "mention").
{% endhint %}

* **Double-check the supported features** - Built-in engine features generally perform better than home-brewed solutions, since they can be tested and iterated on by a large developer base. If you need features like rigid-body physics or photorealistic lighting, it could be better to develop in an engine that supports them by default, like Unity.
* **Be careful with file size** - Without optimization, fully-featured C++ engines like Unity can have much larger application sizes than JavaScript engines like Babylon.js, Three.js, and PlayCanvas. If you don't need all of the features of Unity, using a javascript-based engine may make bundle size optimization easier. Users on mobile devices typically prefer smaller app sizes, as they may be on a cellular network.
* **Device support** - If you plan to support mobile devices, some engines, like Unity and PlayCanvas, support touch controls out of the box. For more minimal engines like Three.js, you will need to use a third-party solution or implement touch controls yourself. If you plan to support XR, make sure your chosen engine has WebXR support.
* **Developer experience matters** - Developers should use an engine that suits their strengths. If you're already comfortable with Unity, then continue with that engine. If you're looking to jump into JavaScript development, but still want an editor, PlayCanvas might be the right choice. For programming specialists, Three.js and Babylon.js provide more flexibility.

***

## Selected Optimized VIVERSE Experiences

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-cover data-type="image">Cover image</th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td>Horizon Chaser</td><td>Impressive graphics and smooth performance</td><td><a href="../.gitbook/assets/horizon-chaser.png">horizon-chaser.png</a></td><td><a href="https://worlds.viverse.com/TAJTZdc">https://worlds.viverse.com/TAJTZdc</a></td></tr><tr><td>Pet Rescue</td><td>A highly detailed world with intuitive controls on multiple devices.</td><td><a href="../.gitbook/assets/pet-rescue.png">pet-rescue.png</a></td><td><a href="https://worlds.viverse.com/MMuLWbE">https://worlds.viverse.com/MMuLWbE</a></td></tr><tr><td>To the Limbs</td><td>An interactive music video that loads a large and immersive world.</td><td><a href="../.gitbook/assets/to-the-limbs.png">to-the-limbs.png</a></td><td><a href="https://worlds.viverse.com/TkZWCbJ">https://worlds.viverse.com/TkZWCbJ</a></td></tr><tr><td>Alfi's Adventures</td><td>Fine-tuned performance across mobile and desktop platforms.</td><td><a href="../.gitbook/assets/alfis-adventures.png">alfis-adventures.png</a></td><td><a href="https://worlds.viverse.com/EcxNxwe">https://worlds.viverse.com/EcxNxwe</a></td></tr></tbody></table>
