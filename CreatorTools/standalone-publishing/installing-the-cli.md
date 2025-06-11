---
description: >-
  The VIVERSE command-line tool provides simple yet powerful functionality to
  manage your VIVERSE content
---

# Installing the CLI

***

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
