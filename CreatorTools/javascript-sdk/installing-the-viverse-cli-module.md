---
description: >-
  This document provides a guide for setting up the VIVERSE CLI Module which is
  a command-line tool for the VIVERSE platform, providing simple yet powerful
  functionality to manage VIVERSE accounts.
---

# Installing the VIVERSE CLI Module

* General description of what is this page
* Prerequisites for using the VIVERSE CLI (node v22)
* Installing the VIVERSE CLI in your account
* Signing in with auth
* A description of usage and how to learn more
* Compatibility with VIVERSE
  * Do you need to use vite.js or can you use another library for building your project?
  * Do you need to use three.js or can you use other packages?
  * Can you do API calls from within your project on VIVERSE?

***

## Pre-Requisites

* Node.js v22.14.0 or later installed



## Installing the VIVERSE CLI in your account

{% stepper %}
{% step %}
### Install the tool using command prompt

A. Inside a command prompt, type: **npm install -g @viverse/cli**, then click Enter. Installing a package with **-g** installs the package globally. The location of globally installed packages depends on your operating system and npm configuration:

* **Windows** : In windows, packages are installed in %APPDATA%\npm\node\_modules .
* **macOS and Linux** : In mac or Linux packages are typically installed in /usr/local/lib/node\_modules or a user-specific directory like \~/.npm-global.

B. Confirm that the command line tool is installed based on screen feedback.

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}



## Signing into VIVERSE with VIVERSE CLI

{% stepper %}
{% step %}
### Login to VIVERSE platform

A. Open a command prompt and type: **viverse-cli auth login**, then click Enter.

B. Enter VIVERSE **email** and **password**.

C. Confirm login was successful.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}

## VIVERSE CLI usage

<table><thead><tr><th width="294">Process</th><th>Command</th></tr></thead><tbody><tr><td>Login to VIVERSE platform</td><td><code>viverse-cli auth login</code></td></tr><tr><td>Non-interactive login for CI/CD integration</td><td><code>viverse-cli auth login -e &#x3C;email> -p &#x3C;password></code></td></tr><tr><td>For CI/CD environments, it's recommended to use environment variables</td><td><code>viverse-cli auth login -e $VVS_EMAIL -p $VVS_PASSWORD</code></td></tr><tr><td>Check authentication status</td><td><code>viverse-cli auth status</code></td></tr><tr><td>Logout</td><td><code>viverse-cli auth logout</code></td></tr><tr><td>Publish content</td><td><code>viverse-cli publish &#x3C;path></code></td></tr><tr><td>View application list</td><td><code>viverse-cli app list</code></td></tr></tbody></table>



## Compatibility with VIVERSE

<details>

<summary>Do you need to use vite.js or can you use another library for building your project?</summary>



</details>

<details>

<summary>Do you need to use three.js or can you use other packages?</summary>

No, VIVERSE CLI can be used with other WebGL frameworks.

</details>

<details>

<summary>Can you do API calls from within your project on VIVERSE?</summary>



</details>



{% stepper %}
{% step %}

{% endstep %}

{% step %}

{% endstep %}
{% endstepper %}
