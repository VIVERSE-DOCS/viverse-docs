---
description: The document provides a guide for setting up the NPM module.
---

# NPM Module Setup

***

{% stepper %}
{% step %}
### Install Node.js

A. Download the latest version of **Node.js (LTS)** from http [https://nodejs.org/en](https://nodejs.org/en).

<figure><img src="../.gitbook/assets/image (654).png" alt="" width="375"><figcaption></figcaption></figure>

B. Use the defaults during the installation, but place a checkmark\
in the **Automatically install the necessary tools** checkbox.

<figure><img src="../.gitbook/assets/image (655).png" alt="" width="304"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Confirm Node.js is installed

A. Open a command prompt and type: **node**, then click Enter.

B. Confirm that Node.js is installed when the following message prints in command prompt: **Welcome to Node.js v##.##.##**.

<figure><img src="../.gitbook/assets/image (656).png" alt="" width="373"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Install Three.js

A. Three.js needs to be installed in the Three.js project folder, if it has not been installed already. Open command prompt and change directories to your Three.js project.

B. Type: **npm install --save three**.

<figure><img src="../.gitbook/assets/image (658).png" alt="" width="375"><figcaption></figcaption></figure>

C. Confirm **node\_modules** folder and **package.json** have been added to the directory.

<figure><img src="../.gitbook/assets/image (657).png" alt="" width="375"><figcaption></figcaption></figure>

D. Confirm the **three** folder folder and **.package-lock.json** have been added to the **node\_modules** directory.

<figure><img src="../.gitbook/assets/image (659).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Install the build tool Vite

A. If choosing to use **Vite** as the build tool, it needs to be installed in the Three.js project folder also. With command prompt opened and the directory set to your Three.js project, type the command: **npm install --save-dev vite**.

<figure><img src="../.gitbook/assets/image (661).png" alt="" width="375"><figcaption></figcaption></figure>

B. Confirm **Vite** has been installed by checking for additional folders in the **node\_modules** folder.

<figure><img src="../.gitbook/assets/image (660).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the Vite build settings

A. Add the Vite build settings to **package.json**.

<figure><img src="../.gitbook/assets/image (662).png" alt="" width="287"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the vite.config.js file

A. Add the **vite.config.js** file to the root of the project.

<figure><img src="../.gitbook/assets/image (663).png" alt="" width="369"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Create a development build

A. To create a development build of the Three.js project, type the following command inside command prompt under the Three.js project directory: **npx vite**.

<figure><img src="../.gitbook/assets/image (665).png" alt="" width="369"><figcaption></figcaption></figure>

B. Confirm the development build of the Three.js project was built successfully when Vite provides a **localhost URL** to test.

<figure><img src="../.gitbook/assets/image (664).png" alt="" width="320"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Test development build

A. To test a development build of the Three.js project, open the browser and navigate to the URL that printed in the previous step. In this example, the URL is http://localhost:5173. Confirm the app works as expected.

<figure><img src="../.gitbook/assets/image (666).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Create a production build

A. To create a production build of the Three.js project, type the following command inside command prompt under the Three.js project directory: **npx vite build**.

<figure><img src="../.gitbook/assets/image (667).png" alt="" width="375"><figcaption></figcaption></figure>

B. Confirm the production build of the Three.js project was built successfully by confirming the **dist** folder was created and populated.

<figure><img src="../.gitbook/assets/image (668).png" alt="" width="375"><figcaption></figcaption></figure>




{% endstep %}
{% endstepper %}
