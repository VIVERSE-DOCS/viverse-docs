---
description: >-
  Learn how to check for and login to VIVERSE services to access user
  information including their preferred avatars
---

# Login & Authentication for the SDK

***

This guide is designed to help creators integrate VIVERSE SDKs when uploading WebGL content from engines like Unity, three.js or Wonderland Engine to&#x20;VIVERSE Studio.

> BEFORE GETTING STARTED:
>
> 1. An App ID needs to be created, either through the CLI or the VIVERSE Studio workflow. [See our docs](https://app.gitbook.com/s/4pMiThqqrBzfvP8uy5am/publishing-with-your-viverse-account#the-viverse-studio-interface) for this information.
> 2.  The VIVERSE SDK is hosted at this URL and must be integrated into your JavaScript/WebGL project and target engine:
>
>     [`https://www.viverse.com/static-assets/viverse-sdk/1.2.9/viverse-sdk.umd.js`>     ](https://www.viverse.com/static-assets/viverse-sdk/1.2.9/viverse-sdk.umd.js)

## Authentication & Authorization

User login is required to check for user information like name, profile picture URL, and .vrm avatar URL.

#### Step 1: **Initialize the Client**

Before any authentication, initialize the SDK client in your application:

```
// Initialize a new client
globalThis.viverseClient = new globalThis.viverse.client({
    clientId: '{yourAppID}',
    domain: 'account.htcvive.com', // HTC Account domain
    cookieDomain: '{yourCookieDomain}', // Optional
});
```

#### Step 2: Check for Existing Authentication

Once the client is initialized, await its `checkAuth()` function to check for valid user credentials:

<pre><code><strong>const result = await globalThis.viverseClient.checkAuth();
</strong></code></pre>

If the user is logged in, you'll get their authentication information in an object structured like so:

```
{
    access_token: string; // The access token to be used in API requests
    account_id: string; // The unique user account ID
    expires_in: number; // Remaining token lifetime in seconds
    state: string; // Optional custom state value from the original login
}
```

If the user is not logged in, the result will come back `undefined`.

#### Step 3: **Trigger Login via VIVERSE Worlds**

If login is required for your experience, an automated login and single sign-on (SSO) workflow is available. To request it, this method can be called, which will forward the user through this login flow within the iframe:

```
globalThis.viverseClient.loginWithWorlds()
```

**Step 4: Handle Post-Login State on Page Load**

You can wrap your logic in an arrow function callback on the window's `load` event to handle the automatic login flow.

```
// this callback will run when the iframe is refreshed
window.addEventListener('load', async () => {
    // reinitialize
    globalThis.viverseClient = new globalThis.viverse.client({
        clientId: '{yourAppID}',
        domain: 'account.htcvive.com', // HTC Account domain
        cookieDomain: '{yourCookieDomain}', // Optional
    });
    // check login status again
    const result = await globalThis.viverseClient.checkAuth();
    
    if (result === undefined) {
        // This will cause a refresh
        globalThis.viverseClient.loginWithWorlds();
    }
    else {
        // `result` contains credentials to make authorized requests 
    }
});
```

If `result` has valid authorization credentials, you can then utilize features like the [Avatar SDK](avatar-sdk.md), Leaderboard SDK and Matchmaking SDK.
