---
description: >-
  This page details the two methods to publish to VIVERSE: VIVERSE Studio and
  our command line interface...
---

# Publishing to VIVERSE



***

## Introduction

You can upload to VIVERSE from all open and web-compatible frameworks, such as ThreeJS, Unity, and more! No matter what you are building with, you can upload and manage your builds easily using VIVERSE studio or our command-line interface (CLI). Once uploaded, VIVERSE worlds are associated with your VIVERSE account and profile located at [https://worlds.viverse.com/profile](https://worlds.viverse.com/profile). This page guides you through the uploading process using both methods!

## Publishing with VIVERSE Studio

Using VIVERSE Studio offers several advantages:

* User-Friendly Interface: The platform is designed to be intuitive, making it easy for users of all skill levels.
* Community Engagement: Connect with other creators and receive feedback on your work.
* Showcase Your Work: The platform provides a space to display your creations to a wider audience.



### Opening VIVERSE Studio

To open VIVERSE Studio from the [Viverse.com](https://viverse.com) landing page

Step 1. Click on your avatar in the upper-right hand corner (A)

Step 2. Click on **VIVERSE Studio** (B)

<figure><img src="../.gitbook/assets/image (748).png" alt="" width="375"><figcaption></figcaption></figure>



### Creating a world using VIVERSE Studio

{% stepper %}
{% step %}
### Start the world creation process

Step 1. Click **Upload** (A)

Step 2. Click **Create New World** (B)

<figure><img src="../.gitbook/assets/image (750).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Fill out the form

Step 1. Enter a name for the world in the **Name** field (A)

Step 2. Enter an optional **Description** for the world (B)

Step 3. Select the checkbox for the **VIVERSE PLATFORM AGREEMENT** (C)

Step 4. Click **Create** (D)

<figure><img src="../.gitbook/assets/image (755).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### View the Overview Tab

The **Overview** tab displays the following information

**App ID** (A)

**Name** (B)

**World Information** (C)

<figure><img src="../.gitbook/assets/image (2) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Upload a zip file on the Content Versions Tab

To upload a project

Step 1. Click on the **Content Versions** tab (A)

Step 2. Click the **Select File** button (B)

Step 3. A window will be displayed that allows selection of a zip file of a project. After selecting the zip file, click the **Upload** button (C). Refer to the [Publishing Content](https://docs.viverse.com/standalone-publishing/publishing-to-viverse-with-the-cli#publishing-content) section for file type restrictions.

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (3) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Adding additional permissions to a project

To add additional permissions to the world, from the **Content Versions** tab

Step 1. Click **Apply iframe Settings** (A)

<figure><img src="../.gitbook/assets/image (4) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>


{% endstep %}

{% step %}
### Adding **Content Behavior Permissions**&#x20;

Expand the **Content Behavior Permissions** dropdown to add the following permissions:

* **allow-forms -** Submit forms
* **allow-modals -** Open modal dialogs
* **allow-popups -** Open popup windows
* **allow-top-navigation -** Navigate top window
* **allow-pointer-lock -** Use pointer lock
* **allow-presentation -** Start presentations
* **allow-downloads -** Download files
* **allow-orientation-lock -** Allow orientation lock
* **allow-popups-to-escape-sandbox -** Allow popups to escape sandbox
* **allow-top-navigation-by-user-activation -** Allow top navigation by user activation

<figure><img src="../.gitbook/assets/image (5) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Adding Device & Sensor Access permissions

Expand the **Device & Sensor Access** dropdown to add the following permissions

* **accelerometer** - Allows access to accelerometer sensor data
* **camera** - Allows access to device camera
* **gyroscope** - Allows access to gyroscope sensor data
* **magnetometer** - Allows access to magnetometer sensor data
* **midi** - Allows access to MIDI devices
* **window-management** - Allows multi-window management
* **xr-spatial-tracking** - Allows access to VR/AR features

<figure><img src="../.gitbook/assets/image (6) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Previewing and Submitting for Review

Step 1. Click **Preview** (A) to view the world with a link that only the creator access.

<figure><img src="../.gitbook/assets/image (762).png" alt="" width="375"><figcaption></figcaption></figure>

Step 2. Click **Guest Preview** (B) to receive a link that can be shared with others for a limited period of time.

Step 3. Click **Submit for Review** (C) to submit your world to be reviewed before being released for public access. Refer to this [link](https://support.viverse.com/hc/en-us/requests/new) if you need help or if you don't receive any feedback.

<figure><img src="../.gitbook/assets/image (763).png" alt="" width="375"><figcaption></figcaption></figure>


{% endstep %}
{% endstepper %}

***

## Publishing with the CLI

The [VIVERSE CLI](https://www.npmjs.com/package/@viverse/cli) can be used to publish any working WebGL build to the VIVERSE platform after an authentication process.

### Installation

Using npm:

```
npm install -g @viverse/cli
```

> **Note:** This CLI requires Node.js version 22.15.0 or higher.

### Authentication

Login to VIVERSE platform:

```
viverse-cli auth login
```

<figure><img src="../.gitbook/assets/image (717).png" alt=""><figcaption></figcaption></figure>

Or directly pass in login credentials for CI/CD integration:

```
viverse-cli auth login -e <email> -p <password>
```

In such CI/CD environments, it's recommended to use environment variables:

```
viverse-cli auth login -e $VVS_EMAIL -p $VVS_PASSWORD
```

After login, check your authentication status:

```
viverse-cli auth status
```

<figure><img src="../.gitbook/assets/image (718).png" alt=""><figcaption></figcaption></figure>

And to logout:

```
viverse-cli auth logout
```

After [install and authentication](../publishing-to-viverse-with-the-cli.md), the VIVERSE CLI can be used to publish any working WebGL build to the VIVERSE platform after an authentication process. When publishing, you'll either access your existing projects, or create a new one.

### Creating Applications

You can use the VIVERSE CLI to create a new application directly:

```
viverse-cli app create
```

<figure><img src="../.gitbook/assets/image (719).png" alt=""><figcaption></figcaption></figure>

Or specify a name:

```
viverse-cli app create --name <application-name>
```

Alternately, you can use the [VIVERSE Studio](https://studio.viverse.com/) workflow to create an application ID:

<figure><img src="../.gitbook/assets/image (721).png" alt=""><figcaption><p>From the Upload section, click the light blue "Create New World" button in the top right to open this modal.</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (722).png" alt=""><figcaption><p>Once created, your App ID will display on the world page.</p></figcaption></figure>

### Listing Applications

Once authenticaed, you can view your account's available application list:

```
viverse-cli app list
```

The output will be displayed in a table format with the following columns:

* ID: Application identifier
* STATE: Application state
* TITLE: Application name
* URL: Application preview URL

### Publishing

Publishing content requires two inputs:

1. **App ID** — the target application to publish to (**required**)
2. **Content path** — the directory containing your content (optional if you're already in that directory)

**Option 1: Specify content path**

```
viverse-cli app publish <path> --app-id <your-app-id>
```

**Option 2: From within content directory**

```
viverse-cli app publish --app-id <your-app-id>
```

> **Note:** The App ID is required. You can use the `viverse-cli app list` command to query your existing application IDs, or view the IDs of newly created applications after using `app create`.

> **Important:** After uploading content successfully, you'll need to visit the Creator Studio website to complete the review and publishing process.

> **Warning:** The `<path>` parameter MUST point to your **build output folder** and NOT your source code folder. Publishing source code folders (containing `src/`, `node_modules/`, or development files like `.tsx`, `.jsx`, `.vue`, `.unity`, etc.) will result in non-functional content and deployment failures.
