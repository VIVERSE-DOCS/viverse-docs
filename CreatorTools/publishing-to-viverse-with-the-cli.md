---
description: >-
  The VIVERSE command-line tool provides simple yet powerful functionality to
  manage your VIVERSE content
---

# Publishing to VIVERSE with the CLI

***

After [install and authentication](publishing-to-viverse-with-the-cli.md), the VIVERSE CLI can be used to publish any working WebGL build to the VIVERSE platform after an authentication process. When publishing, you'll either access your existing projects, or create a new one.

## Creating Applications

You can use the VIVERSE CLI to create a new application directly:

```
viverse-cli app create
```

<figure><img src=".gitbook/assets/image (719).png" alt=""><figcaption></figcaption></figure>

Or specify a name:

```
viverse-cli app create --name <application-name>
```

Alternately, you can use the [VIVERSE Studio](https://studio.viverse.com/) workflow to create an application ID:

<figure><img src=".gitbook/assets/image (721).png" alt=""><figcaption><p>From the Upload section, click the light blue "Create New World" button in the top right to open this modal.</p></figcaption></figure>

<figure><img src=".gitbook/assets/image (722).png" alt=""><figcaption><p>Once created, your App ID will display on the world page.</p></figcaption></figure>

#### **Listing Applications**

Once authenticaed, you can view your account's available application list:

```
viverse-cli app list
```

The output will be displayed in a table format with the following columns:

* ID: Application identifier
* STATE: Application state
* TITLE: Application name
* URL: Application preview URL

## Publishing Content

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

### Content Approval

For security purposes, creators must submit their application for approval

