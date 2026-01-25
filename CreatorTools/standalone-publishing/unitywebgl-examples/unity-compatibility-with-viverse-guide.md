---
description: >-
  Complete guide for configuring Unity projects for VIVERSE, including
  compatibility settings, render pipelines, WebGL builds, and fullscreen
  templates.
hidden: true
noIndex: true
---

# Unity Compatibility with VIVERSE Guide

***

## Overview

This comprehensive guide covers Unity project setup, compatibility, and WebGL build configuration for VIVERSE deployment. It includes Unity version compatibility, render pipeline selection, build settings optimization, fullscreen WebGL template creation, and custom loading screen implementation. The guide ensures your Unity project is properly configured for optimal performance and presentation in VIVERSE.

## Prerequisites

* Unity 2021 LTS or newer (with WebGL Build Support module installed)
* Unity project configured for WebGL builds
* Basic understanding of HTML/CSS/JavaScript (for template customization)
* VIVERSE Studio account (for deployment)

## Build Settings for VIVERSE

{% stepper %}
{% step %}
### Switch to WebGL Platform

1. Open `File → Build Settings...`
2. Select `WebGL` from the platform list
3. Click `Switch Platform` (if not already on WebGL)
4. Wait for Unity to reimport assets for WebGL platform
{% endstep %}

{% step %}
### Configure Player Settings

Navigate to `Edit → Project Settings → Player` or click `Player Settings...` in Build Settings window.

**Company Name & Product Name:**

* Set appropriate values (visible in VIVERSE Studio)
* Product Name appears in browser title and VIVERSE content listings

**Default Icon:**

* Set application icon (recommended: 512×512px PNG)
* Used in VIVERSE content browser
{% endstep %}

{% step %}
### WebGL-Specific Settings

Under `Player Settings → Other Settings`:

**Rendering:**

* **Color Space:** Linear (recommended) or Gamma
* **Auto Graphics API:** Enabled (WebGL 2.0 will be used automatically)
* **Static Batching:** Enabled (improves performance)
* **Dynamic Batching:** Enabled (for small meshes)

**Configuration:**

* **Scripting Backend:** IL2CPP (required for WebGL, no Mono option)
* **Api Compatibility Level:** .NET Standard 2.1 or .NET Framework (verify with your code)
* **Allow 'unsafe' Code:** Disable unless required (improves security)

**Optimization:**

* **Strip Engine Code:** Enabled (reduces build size significantly)
* **Managed Stripping Level:** Medium (recommended) or High (more aggressive)
* **Vertex Compression:** Enabled (reduces memory usage)
* **Optimize Mesh Data:** Enabled
{% endstep %}

{% step %}
### Publishing Settings for VIVERSE

Under `Player Settings → Publishing Settings`:

**Compression Format:**

* **Gzip** - Good balance of compression and compatibility (recommended)
* **Brotli** - Better compression, requires modern browsers
* **Disabled** - Not recommended (larger file sizes)

**Data Caching:**

* **Enabled** - Improves load times for returning users (recommended)

**Name Files As Hashes:**

* **Enabled** - Better browser caching, smaller incremental updates

**Decompression Fallback:**

* **Enabled** - Allows fallback if compression fails (recommended for compatibility)

**Memory Size:**

* Start with **256 MB** for small projects
* Increase to **512 MB** or **1024 MB** for larger projects
* Monitor memory usage and adjust based on your content
* **Note:** Larger memory = longer initial load time
{% endstep %}

{% step %}
### Resolution and Presentation

Under `Player Settings → Resolution and Presentation`:

**Default Canvas Width/Height:**

* Set to your target resolution (e.g., 1920×1080 for desktop)
* VIVERSE supports various resolutions; consider responsive design
* Fullscreen template will scale to fit viewport

**Run In Background:**

* **Enabled** - Recommended for VIVERSE (allows content to continue running)

**Fullscreen Mode:**

* Set to **Fullscreen Window** (fullscreen template handles this)
{% endstep %}
{% endstepper %}



### Additional Resources

* Unity WebGL Documentation: https://docs.unity3d.com/Manual/webgl.html
* VIVERSE Developer Portal: https://worlds.viverse.com/
* Unity WebGL Optimization Guide: https://docs.unity3d.com/Manual/webgl-optimization.html
* HTML5 Fullscreen API: https://developer.mozilla.org/en-US/docs/Web/API/Fullscreen\_API
