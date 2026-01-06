---
description: >-
  Integration example of VIVERSE Avatar SDK with Wonderland Cloud networking for
  multiplayer VR experiences.
---

# Wonderland Engine VIVERSE Example

***

## Introduction

In November 2025, VIVERSE and the Wonderland Engine team collaborated to develop a plugin allowing developers to publish to VIVERSE from directly within the Wonderland Engine editor. In addition, we developed examples of using the VIVERSE Avatar and Account SDKs with Wonderland's multiplayer SDK.

This document is a copy of the Wonderland Engine document covering the plugin: [https://github.com/WonderlandEngine/viverse-example/tree/main](https://github.com/WonderlandEngine/viverse-example/tree/main)

## Overview

This repository is a template project that demonstrates how to integrate the **VIVERSE Avatar SDK** with **Wonderland Cloud** networking. It provides a compact, production-minded workflow to:

* Spawn and persist user data (name, avatar) via the VIVERSE Avatar SDK
* Relay avatar + presence state to Wonderland Cloud for performant, medium-scale multiplayer sessions
* Use spatial audio for immersive multiplayer experiences

> Designed for quick iteration and easy publishing through the VIVERSE / Wonderland tooling.

**Live Demo:** [https://worlds.viverse.com/k99xkYZ](https://worlds.viverse.com/k99xkYZ)

## Features

The example project includes the following key features:

* **VIVERSE Avatar SDK Integration:** Login, avatar spawn, and user metadata management
* **Wonderland Cloud Networking:** Client + server template for multiplayer functionality
* **VIVERSE CLI Plugin:** Simple integration for publishing and previewing projects
* **Example Components:** Pre-configured components showing where to place `appid` and server path
* **Spatial Audio:** Avatar position replication with spatial audio for medium-scale sessions

## Project Structure

The repository contains the following directories and files:

* `js/` - JavaScript/TypeScript source files
* `models/` - 3D model assets
* `plugins/viverse-publish-plugin/` - VIVERSE CLI plugin for publishing
* `server/` - Wonderland Cloud server package
* `shaders/` - Custom shader files
* `static/` - Static assets
* `app.js` - Main application entry point
* `package.json` - Node.js dependencies and scripts
* `tsconfig.json` - TypeScript configuration
* `viversecli.wlp` - VIVERSE CLI configuration file

## Quick Setup (Editor)

{% stepper %}
{% step %}
### Open the plugin

Open the `viversePublishPlugin` inside the Wonderland Editor.
{% endstep %}

{% step %}
### Log In with VIVERSE Credentials
{% endstep %}

{% step %}
### Create Application

Click **Create Application** — this redirects to VIVERSE Studio.
{% endstep %}

{% step %}
### Copy the App ID

In VIVERSE Studio create an app and copy the App ID.
{% endstep %}

{% step %}
### Paste the App ID

Paste the App ID into the `appid` field of the plugin.
{% endstep %}
{% endstepper %}

## Scene Configuration

{% stepper %}
{% step %}
### Navigate to Avatar Component

In the Wonderland Editor hierarchy, navigate to: `Avatar -> VrmDynamic` and select the `VrmDynamic` object.
{% endstep %}

{% step %}
### Expand VIVERSE Provider Component

In the Inspector, expand the **Viverse Provider** component (or `viverse-provider-component`).
{% endstep %}

{% step %}
### Paste the App ID

Locate the `appid` field in the component properties and paste the **App ID** that you copied from VIVERSE Studio.
{% endstep %}

{% step %}
### Verify Configuration

Double-check that the App ID is correctly set in both the VIVERSE Publish Plugin and the `VrmDynamic -> Viverse Provider` component, then save the scene to preserve your changes.
{% endstep %}
{% endstepper %}

## Networking Setup (Wonderland Cloud)

{% stepper %}
{% step %}
### Access Wonderland Cloud

Open your web browser and navigate to: `https://cloud.wonderland.dev/create-server`. Log in with your Wonderland Cloud credentials if required.
{% endstep %}

{% step %}
### Locate Server Package

Locate the server package in your project: `server/wonderland-cloud-example-simple-1.0.0.tgz` and verify the file exists and is not corrupted.
{% endstep %}

{% step %}
### Upload Server Package

In the Wonderland Cloud interface, click **Upload** or **Create Server**, select the file: `server/wonderland-cloud-example-simple-1.0.0.tgz`, and wait for the upload to complete. Wonderland Cloud will process and deploy your server.
{% endstep %}

{% step %}
### Copy Server Path

After the server is created, Wonderland Cloud will display server information. **Copy the server path** that is provided (typically looks like: `wss://your-server-id.wonderland.dev`).
{% endstep %}

{% step %}
### Configure Client Component

Return to the Wonderland Editor, select the `Player` object in the hierarchy, locate the `simple-example-client` component in the Inspector, find the `serverPath` field, paste the **server path** you copied from Wonderland Cloud, and save the scene.
{% endstep %}
{% endstepper %}

## Publish & Preview

{% stepper %}
{% step %}
### Open VIVERSE CLI Plugin

In the Wonderland Editor, open the VIVERSE CLI plugin.
{% endstep %}

{% step %}
### Publish Project

Click **Publish** to build and upload your project. Wait for the publishing process to complete. The plugin will show the status of the upload.
{% endstep %}

{% step %}
### Preview Your Project

After publishing, click **Preview URL** in the plugin. This will open a preview build in your browser. Test all functionality including avatar spawning, multiplayer connectivity, spatial audio, and user interactions.
{% endstep %}

{% step %}
### Submit for Review

In the VIVERSE CLI plugin, click **Submit for Review**. This will open the VIVERSE Creator page for your application. Complete any required information in the VIVERSE Creator interface and review your application settings before submission.
{% endstep %}

{% step %}
### Guest Preview Testing

From the VIVERSE Creator page, use the **Guest Preview** link. Share this link with others for testing. Test multiplayer functionality with multiple devices/accounts, verify that avatars appear correctly for all users, and test spatial audio and networking in production-like conditions.
{% endstep %}

{% step %}
### Final Submission

Once testing is complete and you're satisfied with the build, submit the app for review to list it publicly. Wait for VIVERSE approval. After approval, your application will be available in VIVERSE.
{% endstep %}
{% endstepper %}

## Component Configuration Details

### VIVERSE Provider Component

The `Viverse Provider` component (or `viverse-provider-component`) handles authentication and avatar management:

* **App ID:** Your VIVERSE application identifier (required)
* **Avatar Spawning:** Automatically spawns user avatars based on VIVERSE account
* **User Metadata:** Retrieves and stores user name and avatar data

### Simple Example Client Component

The `simple-example-client` component manages networking:

* **Server Path:** WebSocket URL to your Wonderland Cloud server (required)
* **Connection Management:** Handles connection/disconnection events
* **Avatar Replication:** Syncs avatar positions and states across clients
* **Presence State:** Manages user presence in the multiplayer session

## Troubleshooting

### Avatars Not Appearing

**Problem:** Avatars don't spawn or appear in the scene.

**Solutions:**

* Verify the **App ID** is set correctly in both:
  * The VIVERSE Publish Plugin
  * The `VrmDynamic -> Viverse Provider` component
* Check that the App ID matches the one from VIVERSE Studio
* Ensure you're logged in with a valid VIVERSE account
* Check browser console (F12) for authentication errors
* Verify CORS settings in VIVERSE Studio if using custom domains

### Networking Failures

**Problem:** Multiplayer connectivity issues or connection errors.

**Solutions:**

* Re-check that the uploaded server package matches the server entry on `cloud.wonderland.dev`
* Confirm the server path is pasted correctly in the `simple-example-client` component
* Verify the server path format (should start with `wss://`)
* Check that the server is running and accessible
* Review browser console (F12) for WebSocket connection errors
* Ensure firewall/network settings allow WebSocket connections

### Preview URL Issues

**Problem:** Preview URL doesn't work or shows errors.

**Solutions:**

* Use the plugin **Preview URL** to test cross-device connectivity before submitting
* Check that the build was published successfully
* Verify all required assets are included in the build
* Clear browser cache and try again
* Test in different browsers (Chrome, Firefox, Edge)

### Browser Console Errors

**Problem:** Errors appear in browser console (F12).

**Solutions:**

* Check browser console logs for helpful client-side errors
* Common issues include:
  * **Auth errors:** Verify App ID and VIVERSE credentials
  * **CORS errors:** Check VIVERSE Studio CORS settings
  * **Network errors:** Verify server path and connectivity
* Look for specific error messages and error codes
* Check network tab for failed requests

### Server Upload Issues

**Problem:** Server package upload fails or server doesn't deploy.

**Solutions:**

* Verify the server package file exists: `server/wonderland-cloud-example-simple-1.0.0.tgz`
* Check file size limits on Wonderland Cloud
* Ensure the package is not corrupted
* Try re-uploading the server package
* Check Wonderland Cloud dashboard for server status

### Build and Publishing Issues

**Problem:** Build fails or publishing doesn't complete.

**Solutions:**

* Ensure all required dependencies are installed
* Check that all scene files are saved
* Verify project structure matches expected format
* Review build logs for specific errors
* Try rebuilding the project from scratch
* Check Wonderland Editor console for errors

## Additional Resources

### Documentation Links

* **Wonderland Engine Documentation:** [https://wonderlandengine.com/](https://wonderlandengine.com/)
* **VIVERSE Documentation:** [https://docs.viverse.com/](https://docs.viverse.com/)
* **Wonderland Cloud:** [https://cloud.wonderland.dev/](https://cloud.wonderland.dev/)

### Support

If you need help setting up this template for your project:

* Reach out via Discord (Wonderland Engine community)
* Check the GitHub repository for issues and discussions
* Review the example code in the repository for implementation details

### Contributing

Pull requests are welcome for improvements to this template: [https://github.com/WonderlandEngine/viverse-example/tree/main](https://github.com/WonderlandEngine/viverse-example/tree/main)

* Keep changes small and well-documented
* Open an issue for feature requests or bugs
* Follow the existing code style and structure

## Technical Details

### Project Dependencies

The project uses the following key technologies:

* **Wonderland Engine:** Web-based 3D engine for VR/AR experiences
* **VIVERSE Avatar SDK:** Avatar system and user authentication
* **Wonderland Cloud:** Multiplayer networking infrastructure
* **TypeScript/JavaScript:** Application logic and components

### File Structure Details

* `js/` - Contains component scripts and application logic
* `models/` - 3D models and avatar assets
* `server/` - Wonderland Cloud server implementation
* `plugins/viverse-publish-plugin/` - Editor integration for publishing
* `static/` - Static web assets (images, fonts, etc.)
* `shaders/` - Custom shader code for rendering

### Build Process

1. **Development:** Edit scenes and components in Wonderland Editor
2. **Configuration:** Set App ID and server path in components
3. **Publishing:** Use VIVERSE CLI plugin to build and upload
4. **Deployment:** Preview and submit through VIVERSE Creator

### Multiplayer Architecture

The example uses a client-server architecture:

* **Client:** Wonderland Engine application running in browser
* **Server:** Wonderland Cloud server handling networking
* **Communication:** WebSocket connection for real-time updates
* **State Sync:** Avatar positions and presence state synchronized across clients
