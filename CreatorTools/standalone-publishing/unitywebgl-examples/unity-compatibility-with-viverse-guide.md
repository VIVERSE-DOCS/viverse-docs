---
description: >-
  Complete guide for configuring Unity projects for VIVERSE, including
  compatibility settings, render pipelines, WebGL builds, and fullscreen
  templates.
hidden: true
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

## Unity & VIVERSE Compatibility Overview

VIVERSE supports Unity WebGL builds with specific requirements and recommendations for optimal performance and compatibility. This section covers essential settings and considerations for targeting VIVERSE.

## Unity Version Compatibility

{% stepper %}
{% step %}
### Supported Unity Versions

VIVERSE supports the following Unity versions:

* **Unity 2021 LTS** (2021.3.x) - Recommended for stability
* **Unity 2022 LTS** (2022.3.x) - Recommended for newer features
* **Unity 2023.x** - Supported, but verify compatibility with VIVERSE SDK

**Note:** Always use LTS (Long Term Support) versions for production projects targeting VIVERSE to ensure long-term compatibility and support.
{% endstep %}

{% step %}
### Required Unity Modules

Install the following modules via Unity Hub:

1. **WebGL Build Support** - Essential for building WebGL projects
2. **WebGL Development Build Support** - Recommended for debugging
3. **IL2CPP** - Required for WebGL builds (automatically included with WebGL module)

Verify installation: `Edit → Preferences → External Tools` or check installed modules in Unity Hub.
{% endstep %}
{% endstepper %}

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

## Render Pipeline Compatibility

{% stepper %}
{% step %}
### Supported Render Pipelines

**Important:** Unity WebGL does **NOT** support HDRP (High Definition Render Pipeline).

**Built-in Render Pipeline:**

* Fully supported for WebGL/VIVERSE
* Best compatibility across all browsers
* Recommended for projects targeting maximum compatibility
* No additional packages required

**Universal Render Pipeline (URP):**

* Fully supported for WebGL/VIVERSE
* **Recommended** for new projects targeting WebGL
* Better performance and modern features optimized for web platforms
* Requires URP package (installed via Package Manager)
* Optimized for low-end and web platforms

**High Definition Render Pipeline (HDRP):**

* **NOT supported** on WebGL/VIVERSE
* HDRP is designed for high-end GPUs and requires features unavailable in WebGL:
  * Real-time ray tracing
  * Advanced volumetrics
  * Compute shaders with full GPU access
  * Large render targets
  * Advanced shader models
* WebGL runs in a browser sandbox with restricted GPU access and memory
* **You must convert HDRP projects to URP for WebGL builds**
{% endstep %}

{% step %}
### Choosing a Render Pipeline

**For New Projects Targeting WebGL/VIVERSE:**

* **URP** is **strongly recommended** for modern features and optimal WebGL performance
* Install via `Window → Package Manager → Unity Registry → Universal RP`

**For Existing Projects:**

* **Built-in:** Works without changes, but URP offers better WebGL performance
* **URP Migration:** Use Unity's Render Pipeline Converter tool (`Edit → Render Pipeline → Universal Render Pipeline → Convert Project to URP`)
* **HDRP Projects:** **Must convert to URP** for WebGL builds (HDRP is not supported)

**Converting HDRP to URP for WebGL:**

1. Install URP package via Package Manager
2. Use Unity's Render Pipeline Converter:
   * `Edit → Render Pipeline → Universal Render Pipeline → Convert Project to URP`
3. Review and update materials (HDRP materials need URP equivalents)
4. Test shaders and lighting (some HDRP features may not have direct URP equivalents)
5. Optimize for WebGL performance (WebGL is sensitive to draw calls, textures, and shader complexity)

**Considerations:**

* URP provides better WebGL performance than Built-in
* URP has smaller build sizes
* URP supports modern shader features optimized for web
* Built-in has maximum compatibility but lower performance
* **HDRP cannot be used for WebGL builds**
{% endstep %}

{% step %}
### Render Pipeline Settings

**Built-in Render Pipeline:**

* No special configuration needed
* Settings in `Edit → Project Settings → Graphics`
* Configure Quality Settings as needed

**Universal Render Pipeline (URP):**

* Create URP Asset: `Assets → Create → Rendering → URP Asset`
* Assign in `Edit → Project Settings → Graphics → Scriptable Render Pipeline Settings`
* Configure URP Asset settings:
  * **Render Scale:** 1.0 (or lower for performance)
  * **HDR:** Disabled (WebGL doesn't support HDR)
  * **MSAA:** 2x or 4x (balance quality vs performance)
  * **Shadow Distance:** Adjust based on your scene needs
{% endstep %}

{% step %}
### Shader Compatibility

**Built-in Shaders:**

* All built-in shaders work with Built-in Render Pipeline
* Standard, Unlit, UI shaders fully supported

**URP Shaders:**

* Use URP-compatible shaders (Lit, Unlit, etc.)
* Built-in shaders won't work with URP
* Convert shaders using Shader Graph or manually rewrite
* **HDRP shaders are NOT compatible** - must convert to URP shaders for WebGL

**Custom Shaders:**

* Ensure shaders are compatible with your chosen render pipeline
* **HDRP shaders cannot be used in WebGL builds** - convert to URP equivalents
* Test shaders in WebGL build (some features may not work)
* WebGL 2.0 supports most modern shader features, but not HDRP-specific features
* WebGL is sensitive to shader complexity - optimize for performance
{% endstep %}
{% endstepper %}

## WebGL Template - Fullscreen Canvas

{% stepper %}
{% step %}
### Understanding WebGL Templates

WebGL templates control the HTML/CSS/JavaScript wrapper around your Unity WebGL build. They determine:

* How the canvas is displayed (fullscreen, windowed, etc.)
* Loading screen appearance and behavior
* Browser UI elements visibility
* Mobile responsiveness
* Fullscreen functionality

**Default Templates:**

* Unity provides default templates (Minimal, Default, etc.)
* Custom templates allow full control over presentation
{% endstep %}

{% step %}
### Fullscreen Canvas Benefits

A fullscreen canvas template provides:

* **Immersive Experience:** No browser UI distractions
* **Maximum Screen Usage:** Canvas fills entire viewport
* **Better Performance:** No layout calculations for browser elements
* **Professional Appearance:** Clean, game-like presentation
* **VIVERSE Optimization:** Matches VIVERSE's immersive environment

**Key Features:**

* Canvas set to 100% width and height
* Body and container with `overflow: hidden`
* No scrollbars or browser chrome
* Responsive to different screen sizes
* Mobile-friendly viewport configuration
{% endstep %}

{% step %}
### Custom Loading Screen Overview

Custom loading screens enhance user experience by:

* **Branding:** Display your logo/branding during load
* **Progress Feedback:** Show loading progress to users
* **Professional Appearance:** Custom design matching your project
* **User Engagement:** Keep users informed during asset loading

**Components:**

* Loading bar container (centered on screen)
* Logo/branding element
* Progress bar (animates from 0% to 100%)
* Optional loading text or animations
* Styled with CSS for custom appearance
{% endstep %}
{% endstepper %}

## Step 1. Create the WebGL Template Folder Structure

{% stepper %}
{% step %}
### Navigate to Assets Folder

In Unity, open your project and navigate to the `Assets` folder in the Project window.
{% endstep %}

{% step %}
### Create WebGLTemplates Directory

1. Right-click in the `Assets` folder → Create → Folder
2. Name the folder `WebGLTemplates` (exact name required by Unity)
3. This folder will contain all custom WebGL templates
{% endstep %}

{% step %}
### Create FullScreen Template Folder

1. Right-click on `WebGLTemplates` → Create → Folder
2. Name it `FullScreen` (this will be the template name visible in Build Settings)
3. Unity will automatically detect this folder as a WebGL template
{% endstep %}
{% endstepper %}

## Step 2. Create the Fullscreen HTML Template

{% stepper %}
{% step %}
### Create index.html File

1. Right-click on the `FullScreen` folder → Show in Explorer (Windows) or Reveal in Finder (Mac)
2. Create a new text file named `index.html` (not `index.html.txt`)
3. This file will serve as the main HTML template for your WebGL build
{% endstep %}

{% step %}
### Add Fullscreen HTML Structure

Open `index.html` and add the following fullscreen template code:

```html
<!DOCTYPE html>
<html lang="en-us">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <title>Unity WebGL Player | {{{ PRODUCT_NAME }}}</title>
    <style>
      body {
        padding: 0;
        margin: 0;
        overflow: hidden;
      }
      #unity-container {
        position: absolute;
        width: 100%;
        height: 100%;
        overflow: hidden;
      }
      #unity-canvas {
        background: #231F20;
      }
      #unity-loading-bar {
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
        display: none;
      }
      #unity-logo {
        width: 154px;
        height: 130px;
        background: url('unity-logo-dark.png') no-repeat center;
      }
      #unity-progress-bar-empty {
        width: 141px;
        height: 18px;
        margin-top: 10px;
        margin-left: 6.5px;
        background: url('progress-bar-empty-dark.png') no-repeat center;
      }
      #unity-progress-bar-full {
        width: 0%;
        height: 18px;
        margin-top: 10px;
        background: url('progress-bar-full-dark.png') no-repeat center;
      }
      #unity-footer {
        position: relative;
        display: none;
      }
      .unity-mobile #unity-footer {
        display: none;
      }
      #unity-webgl-logo {
        float: left;
        width: 204px;
        height: 38px;
        background: url('webgl-logo.png') no-repeat center;
      }
      #unity-build-title {
        float: right;
        margin-right: 10px;
        line-height: 38px;
        font-family: arial;
        font-size: 18px;
      }
      #unity-fullscreen-button {
        cursor: pointer;
        float: right;
        width: 38px;
        height: 38px;
        background: url('fullscreen-button.png') no-repeat center;
      }
      #unity-warning {
        position: absolute;
        left: 50%;
        top: 5%;
        transform: translate(-50%);
        background: white;
        padding: 10px;
        display: none;
      }
    </style>
  </head>
  <body>
    <div id="unity-container" class="unity-desktop">
      <canvas id="unity-canvas" tabindex="-1"></canvas>
      <div id="unity-loading-bar">
        <div id="unity-logo"></div>
        <div id="unity-progress-bar-empty">
          <div id="unity-progress-bar-full"></div>
        </div>
      </div>
      <div id="unity-warning"> </div>
      <div id="unity-footer">
        <div id="unity-webgl-logo"></div>
        <div id="unity-fullscreen-button"></div>
        <div id="unity-build-title">{{{ PRODUCT_NAME }}}</div>
      </div>
    </div>
    <script>
      var container = document.querySelector("#unity-container");
      var canvas = document.querySelector("#unity-canvas");
      var loadingBar = document.querySelector("#unity-loading-bar");
      var progressBarFull = document.querySelector("#unity-progress-bar-full");
      var fullscreenButton = document.querySelector("#unity-fullscreen-button");
      var warningBanner = document.querySelector("#unity-warning");

      function unityShowBanner(msg, type) {
        function updateBannerVisibility() {
          warningBanner.style.display = warningBanner.children.length ? 'block' : 'none';
        }
        var div = document.createElement('div');
        div.innerHTML = msg;
        warningBanner.appendChild(div);
        if (type == 'error') div.style = 'background: red; padding: 10px;';
        else {
          if (type == 'warning') div.style = 'background: yellow; padding: 10px;';
          setTimeout(function() {
            warningBanner.removeChild(div);
            updateBannerVisibility();
          }, 5000);
        }
        updateBannerVisibility();
      }

      var buildUrl = "Build";
      var loaderUrl = buildUrl + "/{{{ LOADER_FILENAME }}}";
      var config = {
        dataUrl: buildUrl + "/{{{ DATA_FILENAME }}}",
        frameworkUrl: buildUrl + "/{{{ FRAMEWORK_FILENAME }}}",
        codeUrl: buildUrl + "/{{{ CODE_FILENAME }}}",
#if MEMORY_FILENAME
        memoryUrl: buildUrl + "/{{{ MEMORY_FILENAME }}}",
#endif
#if SYMBOLS_FILENAME
        symbolsUrl: buildUrl + "/{{{ SYMBOLS_FILENAME }}}",
#endif
        streamingAssetsUrl: "StreamingAssets",
        companyName: "{{{ COMPANY_NAME }}}",
        productName: "{{{ PRODUCT_NAME }}}",
        productVersion: "{{{ PRODUCT_VERSION }}}",
        showBanner: unityShowBanner,
      };

      if (/iPhone|iPad|iPod|Android/i.test(navigator.userAgent)) {
        var meta = document.createElement('meta');
        meta.name = 'viewport';
        meta.content = 'width=device-width, height=device-height, initial-scale=1.0, user-scalable=no, shrink-to-fit=yes';
        document.getElementsByTagName('head')[0].appendChild(meta);
        container.className = "unity-mobile";
        canvas.className = "unity-mobile";
      } else {
        canvas.style.width = "100%";
        canvas.style.height = "100%";
      }

      loadingBar.style.display = "block";

      var script = document.createElement("script");
      script.src = loaderUrl;
      script.onload = () => {
        createUnityInstance(canvas, config, (progress) => {
          progressBarFull.style.width = 100 * progress + "%";
        }).then((unityInstance) => {
          loadingBar.style.display = "none";
          fullscreenButton.onclick = () => {
            unityInstance.SetFullscreen(1);
          };
        }).catch((message) => {
          alert(message);
        });
      };

      document.body.appendChild(script);
    </script>
  </body>
</html>
```

**Key Features:**

* `body` and `#unity-container` set to 100% width/height with `overflow: hidden` for true fullscreen
* Canvas fills entire viewport (`width: 100%`, `height: 100%`)
* Loading bar centered on screen during asset loading
* Footer hidden by default (can be shown if needed)
* Mobile-responsive viewport meta tag
{% endstep %}

{% step %}
### Refresh Unity Project

1. Return to Unity Editor
2. The `index.html` file should appear in the Project window under `Assets/WebGLTemplates/FullScreen/`
3. If not visible, right-click in Project window → Refresh, or press `Ctrl+R` (Windows) / `Cmd+R` (Mac)
{% endstep %}
{% endstepper %}

## Step 3. Customize the Loading Screen

{% stepper %}
{% step %}
### Understanding the Loading Screen Elements

The template includes several customizable elements:

* `#unity-loading-bar`: Container for loading screen elements
* `#unity-logo`: Logo image displayed during loading
* `#unity-progress-bar-empty`: Background of the progress bar
* `#unity-progress-bar-full`: Animated progress bar that fills from 0% to 100%

All elements are centered on screen using CSS transforms.
{% endstep %}

{% step %}
### Replace Default Loading Images

1. In the `FullScreen` folder, replace the default Unity loading images with your custom assets:
   * `unity-logo-dark.png` (154×130px recommended) - Your logo/branding
   * `progress-bar-empty-dark.png` (141×18px) - Progress bar background
   * `progress-bar-full-dark.png` (141×18px) - Progress bar fill image
2. Alternatively, modify the CSS in `index.html` to use different image paths or create custom loading UI with HTML/CSS instead of images.
{% endstep %}

{% step %}
### Custom Loading Screen with HTML/CSS

To create a fully custom loading screen without images, modify the `#unity-loading-bar` section:

```html
<div id="unity-loading-bar">
  <div id="custom-logo" style="width: 200px; height: 200px; margin: 0 auto; background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); border-radius: 20px; display: flex; align-items: center; justify-content: center; color: white; font-size: 48px; font-weight: bold;">
    YOUR LOGO
  </div>
  <div id="custom-progress-container" style="width: 300px; height: 20px; margin: 20px auto; background: rgba(255,255,255,0.2); border-radius: 10px; overflow: hidden;">
    <div id="unity-progress-bar-full" style="width: 0%; height: 100%; background: linear-gradient(90deg, #667eea 0%, #764ba2 100%); transition: width 0.3s ease;"></div>
  </div>
  <div id="loading-text" style="text-align: center; color: white; font-family: Arial; font-size: 18px; margin-top: 10px;">
    Loading...
  </div>
</div>
```

Update the JavaScript progress callback to also update text:

```javascript
createUnityInstance(canvas, config, (progress) => {
  progressBarFull.style.width = 100 * progress + "%";
  var loadingText = document.querySelector("#loading-text");
  if (loadingText) {
    loadingText.textContent = "Loading... " + Math.round(progress * 100) + "%";
  }
})
```
{% endstep %}

{% step %}
### Add Custom Loading Animations

Enhance the loading screen with CSS animations. Add to the `<style>` section:

```css
@keyframes pulse {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.5; }
}

#unity-logo {
  animation: pulse 2s ease-in-out infinite;
}

@keyframes slideIn {
  from { transform: translateX(-100%); }
  to { transform: translateX(0); }
}

#unity-progress-bar-full {
  transition: width 0.3s ease;
  animation: slideIn 0.5s ease-out;
}
```
{% endstep %}
{% endstepper %}

## Step 4. Configure Build Settings

{% stepper %}
{% step %}
### Open Build Settings

1. In Unity, go to `File → Build Settings...`
2. Select `WebGL` from the Platform list
3. Click `Switch Platform` if not already on WebGL (this may take a few minutes)
{% endstep %}

{% step %}
### Select the FullScreen Template

1. Click `Player Settings...` (or go to `Edit → Project Settings → Player`)
2. In the Player Settings window, expand the `Publishing Settings` section
3. Under `WebGL Template`, select `FullScreen` from the dropdown
4. The template you created should now be available in this list
{% endstep %}

{% step %}
### Configure Additional WebGL Settings

For optimal VIVERSE deployment, configure these settings:

**Resolution and Presentation:**

* Default Canvas Width: 1920 (or your target resolution)
* Default Canvas Height: 1080 (or your target resolution)
* Run In Background: Enabled (recommended for VIVERSE)

**Publishing Settings:**

* Compression Format: Gzip or Brotli (for smaller file sizes)
* Data caching: Enabled (improves load times for returning users)
* Name Files As Hashes: Enabled (better caching)

**Other Settings:**

* Strip Engine Code: Enabled (reduces build size)
* Managed Stripping Level: Medium or High (further reduces size)
{% endstep %}
{% endstepper %}

## Step 5. Build the WebGL Project

{% stepper %}
{% step %}
### Prepare for Build

1. Ensure your scene is saved (`Ctrl+S` / `Cmd+S`)
2. In Build Settings, verify the scenes you want to include are checked
3. Click `Add Open Scenes` if your current scene isn't listed
{% endstep %}

{% step %}
### Build the Project

1. Click `Build` in the Build Settings window
2. Choose or create an output folder (e.g., `Builds/WebGL`)
3. Click `Select Folder`
4. Unity will compile and build your project (this may take several minutes)
5. Once complete, navigate to the build output folder
{% endstep %}

{% step %}
### Verify Build Output

Your build folder should contain:

* `index.html` - Your custom fullscreen template
* `Build/` folder - Contains Unity WebGL build files (.data, .framework.js, .loader.js, etc.)
* `TemplateData/` folder (if using default template) - Contains template assets
* `StreamingAssets/` folder (if you have streaming assets) - Contains additional assets

The `index.html` should be your custom fullscreen template with the loading screen.
{% endstep %}
{% endstepper %}

## Step 6. Test the Fullscreen Build Locally

{% stepper %}
{% step %}
### Set Up Local Server

WebGL builds require a web server to run (cannot open `index.html` directly due to CORS restrictions).

**Option 1: Python HTTP Server (if Python installed):**

```bash
cd path/to/your/build/folder
python -m http.server 8000
```

Then open `http://localhost:8000` in your browser.

**Option 2: Node.js HTTP Server:**

```bash
npm install -g http-server
cd path/to/your/build/folder
http-server -p 8000
```

**Option 3: Unity's Built-in Server:** After building, Unity may offer to open a local server automatically.
{% endstep %}

{% step %}
### Test Fullscreen Functionality

1. Open the build in your browser
2. Verify the loading screen appears and progress bar animates
3. Once loaded, the canvas should fill the entire browser window
4. Test the fullscreen button (if visible) or press `F11` for browser fullscreen
5. Verify no browser UI elements are visible (address bar, scrollbars, etc.)
6. Test on different screen resolutions and aspect ratios
{% endstep %}

{% step %}
### Test Mobile Responsiveness

1. Open browser developer tools (`F12`)
2. Enable device emulation mode
3. Test on various mobile device presets (iPhone, Android, tablets)
4. Verify the viewport meta tag works correctly
5. Test touch interactions if your project uses them
{% endstep %}
{% endstepper %}

## Step 7. Deploy to VIVERSE

{% stepper %}
{% step %}
### Prepare Build for Upload

1. Navigate to your build output folder
2. Select all files and folders (`index.html`, `Build/`, `StreamingAssets/` if present)
3. Create a ZIP archive of these files
4. Name it appropriately (e.g., `MyProject_WebGL_Build.zip`)
5. Verify the ZIP contains the root `index.html` file (not nested in a subfolder)
{% endstep %}

{% step %}
### Upload to VIVERSE Studio

1. Log in to VIVERSE Studio (https://worlds.viverse.com/)
2. Navigate to `Manage Content` or your project dashboard
3. Click `Upload` or `New Content`
4. Select your ZIP file
5. Wait for upload and processing to complete
{% endstep %}

{% step %}
### Configure VIVERSE Settings

1. In VIVERSE Studio, configure your content settings:
   * Set appropriate title and description
   * Configure access permissions (public/private)
   * Set thumbnail/preview image
   * Configure any required VIVERSE SDK settings
2. Ensure WebGL compatibility is enabled
{% endstep %}

{% step %}
### Preview and Publish

1. Use VIVERSE Studio's preview feature to test your content
2. Verify fullscreen behavior works correctly in VIVERSE environment
3. Test loading screen appearance and timing
4. Once satisfied, submit for approval/publishing
5. After approval, your content will be available in VIVERSE
{% endstep %}
{% endstepper %}

## Step 8. Advanced Customization

{% stepper %}
{% step %}
### Customize Background Color

Modify the canvas background in the CSS:

```css
#unity-canvas {
  background: #231F20; /* Change to your desired color */
  /* Or use a gradient: */
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}
```
{% endstep %}

{% step %}
### Add Splash Screen or Branding

Add custom HTML before the canvas loads:

```html
<div id="splash-screen" style="position: absolute; width: 100%; height: 100%; background: #000; display: flex; align-items: center; justify-content: center; z-index: 1000;">
  <div style="text-align: center; color: white;">
    <h1>Your Game Title</h1>
    <p>Loading...</p>
  </div>
</div>
```

Then hide it in JavaScript after Unity loads:

```javascript
.then((unityInstance) => {
  loadingBar.style.display = "none";
  var splash = document.querySelector("#splash-screen");
  if (splash) splash.style.display = "none";
  // ... rest of code
})
```
{% endstep %}

{% step %}
### Implement Custom Error Handling

Enhance error messages for better user experience:

```javascript
.catch((message) => {
  // Hide loading screen
  loadingBar.style.display = "none";
  
  // Show custom error UI
  var errorDiv = document.createElement('div');
  errorDiv.style.cssText = 'position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); background: rgba(0,0,0,0.9); color: white; padding: 40px; border-radius: 10px; text-align: center; z-index: 1000;';
  errorDiv.innerHTML = '<h2>Failed to Load</h2><p>' + message + '</p><button onclick="location.reload()" style="margin-top: 20px; padding: 10px 20px; background: #667eea; color: white; border: none; border-radius: 5px; cursor: pointer;">Retry</button>';
  document.body.appendChild(errorDiv);
});
```
{% endstep %}

{% step %}
### Optimize Loading Performance

1. **Enable Compression:** In Player Settings → Publishing Settings, use Gzip or Brotli compression
2. **Reduce Build Size:** Enable code stripping and remove unused assets
3. **Use Addressables:** For large projects, consider Unity Addressables for on-demand loading
4. **Optimize Assets:** Compress textures, reduce polygon counts, optimize audio files
5. **CDN Hosting:** Host your build on a CDN for faster global loading times
{% endstep %}
{% endstepper %}

### Troubleshooting

**Template Not Appearing in Build Settings:**

* Ensure the folder is named exactly `WebGLTemplates` (case-sensitive)
* Ensure `index.html` is directly in the template folder (e.g., `Assets/WebGLTemplates/FullScreen/index.html`)
* Refresh Unity project (`Ctrl+R` / `Cmd+R`)
* Restart Unity Editor

**Loading Screen Not Showing:**

* Check browser console for JavaScript errors (`F12`)
* Verify image paths in CSS are correct
* Ensure `loadingBar.style.display = "block"` is called before Unity loads
* Check that progress callback is properly connected

**Fullscreen Not Working:**

* Verify canvas CSS has `width: 100%` and `height: 100%`
* Check that `body` and container have `overflow: hidden`
* Test in different browsers (Chrome, Firefox, Edge)
* Some browsers require user interaction before allowing fullscreen API

**Build Too Large:**

* Enable compression in Publishing Settings
* Increase Managed Stripping Level
* Remove unused assets and scripts
* Consider using Unity Addressables for large content
* Compress textures and audio files

**VIVERSE Upload Issues:**

* Ensure ZIP contains `index.html` at root level
* Verify all required files are included (Build folder, etc.)
* Check file size limits in VIVERSE Studio
* Ensure WebGL build target is correct (not IL2CPP if not supported)

### Additional Resources

* Unity WebGL Documentation: https://docs.unity3d.com/Manual/webgl.html
* VIVERSE Developer Portal: https://worlds.viverse.com/
* Unity WebGL Optimization Guide: https://docs.unity3d.com/Manual/webgl-optimization.html
* HTML5 Fullscreen API: https://developer.mozilla.org/en-US/docs/Web/API/Fullscreen\_API
