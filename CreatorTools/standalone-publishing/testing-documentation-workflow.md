---
hidden: true
---

# Testing Documentation Workflow

## VIVERSE World Creation Workflow

This document outlines the steps to create and upload a world in VIVERSE using the VIVERSE Studio.

### Step 1: Accessing User Options

* Click on the "user" icon in the top right corner of the VIVERSE interface. This opens a menu with user-related options.

<figure><img src="../.gitbook/assets/image (33).png" alt="" width="375"><figcaption></figcaption></figure>

### Step 2: Navigating to VIVERSE Studio

* In the user menu, click on "VIVERSE Studio". This will redirect you to the VIVERSE Studio interface, where you can manage and upload worlds.

<figure><img src="../.gitbook/assets/image (32).png" alt="" width="375"><figcaption></figcaption></figure>

### Step 3: Upload Section

*   In the VIVERSE Studio, click on the "Upload" icon in the left navigation bar. This will take you to the content management area.

    **Navigation Items:**

    * Dashboard: Takes you to the dashboard
    * Worlds: Takes you to the list of created worlds
    * Upload: Takes you to the Upload Section to upload new content
    * Settings: Takes you to Settings.

    <figure><img src="../.gitbook/assets/image (31).png" alt="" width="248"><figcaption></figcaption></figure>

### Step 4: Creating a New World

*   In the Content Management interface, click the "Create New World" button, usually located in the top right corner.

    **Fields:**

    * Search content...: (Editable) Use the search bar to search for specific content.
    * Content Title: (Read Only) Display the title of the content.
    * Content Status: (Read Only) Shows the status of the content.
    * Access (World Visibility): (Read Only) Shows who has access to the world
    * Manage Content: Button to manage the content.

    <figure><img src="../.gitbook/assets/image (30).png" alt="" width="375"><figcaption></figcaption></figure>

### Step 5: Defining World Details

*   A modal window will appear, prompting you to fill in the details for your new world.

    * Name: Enter a name for your world (up to 30 characters). (Editable)
    * Description: Enter a description for your world (up to 500 characters). (Editable)
    * Check the box to agree to the VIVERSE PLATFORM AGREEMENT.
    * Click the "Create" button to create the world.

    <figure><img src="../.gitbook/assets/image (29).png" alt="" width="375"><figcaption></figcaption></figure>

### Step 6: Overview Tab

*   After creating the world, you'll be directed to the "Overview" tab for the world.

    **Fields:**

    * App ID: (Read Only) This ID uniquely identifies your world and is required for SDK-based features.
    * Name: (Read Only) This is the name of the world.
    * World information: This section links to World settings where you can manage the world description and thumbnail.

    <figure><img src="../.gitbook/assets/image (28).png" alt="" width="375"><figcaption></figcaption></figure>

### Step 7: Content Versions Tab

*   Navigate to the "Content Versions" tab.

    **Elements:**

    * Published Version: Indicates the current live version of the world.
    * Current Pipeline: Displays the pipeline stages.
      * Draft: Where you can upload new content.
      * Preview: To see the preview of content.
      * Under Review: When the content is being reviewed.
      * Published: Content is published.
    * Upload Content: Upload your world content here.

    <figure><img src="../.gitbook/assets/image (27).png" alt="" width="375"><figcaption></figcaption></figure>

### Step 8: Selecting the World File

* In the "Content Versions" tab, click the "Select File" button to choose the world content file (must be a .zip file, max 1 GB).

<figure><img src="../.gitbook/assets/image (26).png" alt="" width="375"><figcaption></figcaption></figure>

### Step 9: Uploading the World

* After selecting the file, click the "Upload" button to upload the world content.

<figure><img src="../.gitbook/assets/image (25).png" alt="" width="375"><figcaption></figcaption></figure>

### Step 10: Upload Confirmation

* A notification will appear, indicating that the upload was successful and the content is being processed. The new content will be available in a few minutes.

<figure><img src="../.gitbook/assets/image (24).png" alt="" width="202"><figcaption></figcaption></figure>

### Step 11: SDK Settings Tab

* The "SDK Settings" tab allows you to configure settings related to the Software Development Kit (SDK) for your world. This may include API keys, authentication settings, and other configurations necessary for your world to interact with VIVERSE services. _(Further details and screenshots for this tab are not available in the provided documentation.)_

<figure><img src="../.gitbook/assets/image (34).png" alt="" width="375"><figcaption></figcaption></figure>

### Step 12: Applying Iframe Settings

* Click "Apply iframe Settings" to configure permissions for the preview environment.

<figure><img src="../.gitbook/assets/image (35).png" alt="" width="375"><figcaption></figcaption></figure>

### Step 13: Content Behavior Permissions

*   In the "Content Behavior Permissions" screen, select the necessary permissions to test your content properly in an iframe.

    * allow-forms: Submit forms
    * allow-modals: Open modal dialogs
    * allow-popups: Open popup windows
    * allow-top-navigation: Navigate top window
    * allow-pointer-lock: Use pointer lock
    * allow-presentation: Start presentations
    * allow-downloads: Download files
    * allow-orientation-lock: Allow orientation lock
    * allow-popups-to-escape-sandbox: Allow popups to escape sandbox
    * allow-top-navigation-by-user-activation: Allow top navigation by user activation

    <figure><img src="../.gitbook/assets/image (36).png" alt="" width="375"><figcaption></figcaption></figure>

### Step 14: Device & Sensor Access

*   In the "Device & Sensor Access" screen, select the necessary permissions to access device sensors.

    * accelerometer: Allows access to accelerometer sensor data
    * camera: Allows access to device camera
    * gyroscope: Allows access to gyroscope sensor data
    * magnetometer: Allows access to magnetometer sensor data
    * midi: Allows access to MIDI devices
    * window-management: Allows multi-window management
    * xr-spatial-tracking: Allows access to VR/AR features

    <figure><img src="../.gitbook/assets/image (20).png" alt="" width="375"><figcaption></figcaption></figure>

### Step 15: Applying Iframe Settings

* Click "Apply iframe Settings" to save the selected permissions.

<figure><img src="../.gitbook/assets/image (19).png" alt="" width="375"><figcaption></figcaption></figure>

### Step 16: Previewing the World

* Click the "Preview" button to view the uploaded world.

<figure><img src="../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

### Step 17: Viewing the App

* The world will load, allowing you to view and test the app.

<figure><img src="../.gitbook/assets/image (37).png" alt="" width="375"><figcaption></figcaption></figure>
