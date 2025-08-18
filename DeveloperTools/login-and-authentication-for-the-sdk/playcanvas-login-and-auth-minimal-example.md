---
description: >-
  Learn how to add VIVERSE login and authentication features into your
  standalone PlayCanvas game
---

# PlayCanvas Login & Auth minimal example

### Pre-requisite: Create a World and App ID in VIVERSE Studio

All SDK usage requires an App ID tied to a specific VIVERSE World, which can be created via [VIVERSE Studio](https://studio.viverse.com/upload). This process is described in detail in [our documentation on VIVERSE Studio](https://app.gitbook.com/s/4pMiThqqrBzfvP8uy5am/publishing-with-your-viverse-account) — but simply create a new app and copy its App ID to get started.

> _**NOTE:** VIVERSE SDKs cannot be used with projects published via the PlayCanvas Create SDK extension, which do not have App IDs._

<div><figure><img src="../.gitbook/assets/Screenshot 2025-08-07 144044.png" alt=""><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/Screenshot 2025-08-07 144325.png" alt=""><figcaption></figcaption></figure></div>

### Step 1: Create a new PlayCanvas project and add the VIVERSE SDK as an external script

In a new PlayCanvas project, go to the project Settings, and in the EXTERNAL SCRIPTS menu, add one URL entry, and point it  to [`https://www.viverse.com/static-assets/viverse-sdk/index.umd.cjs`](https://www.viverse.com/static-assets/viverse-sdk/index.umd.cjs) as in this screenshot. This will ensure the VIVERSE SDK is loaded first and that your PlayCanvas logic has full access to its global methods.

<figure><img src="../.gitbook/assets/image (15).png" alt="" width="153"><figcaption></figcaption></figure>

### Step 2: Initialize the SDK in a new .mjs script

Add a new script called `viverse-manager.mjs` in the project to handle all VIVERSE SDK services and authentication. This process is described generically in our documentation, [**Login & Authentication for the SDK**](./), but here is how it would apply to a modular PlayCanvas script.

```javascript
import { Script, Entity } from "playcanvas";

export class ViverseManager extends Script {
  static scriptName = "viverseManager";
  
  initialize() {
    this.appId = "gqvuyd5duu"  // get this from VIVERSE Studio
    this.profile = null  // we'll use this soon
    this.accessToken = null  // this, too
    
    this.loadViverse()
  }

  async loadViverse() {
    // Create a new viverseClient instance on globalThis
    globalThis.viverseClient = new globalThis.viverse.client({
      clientId: this.appId,  // 
      domain: 'account.htcvive.com',  // HTC Account domain
    })
  }
}
```

### Step 3: Check whether users are logged in

Once the `viverseClient` is instantiated, use its `checkAuth()` function to determine whether the user is logged in. It will return `undefined` if they are **not** logged-in, or credentials if they are.

```javascript
async loadViverse() {
  // Create a new viverseClient instance on globalThis
  globalThis.viverseClient = new globalThis.viverse.client({
    clientId: this.appId,
    domain: 'account.htcvive.com',  // HTC Account domain
  })
  
  // check login status again
  const result = await globalThis.viverseClient.checkAuth()
  
  if (result === undefined) {  // Not logged in
    // We'll come back and add a login function
  } else {
    // Since the user is logged in, get the token to start making requests;
    this.accessToken = await globalThis.viverseClient.getToken()

    if (this.accessToken == undefined) {
      console.warn("Sanity-check - should have an accessToken at this point.")
    } else {
      // initialize avatar client instance
      globalThis.avatarClient = new globalThis.viverse.avatar({
          baseURL: 'https://sdk-api.viverse.com/',  // VIVERSE API domain
          token: this.accessToken,  // required to query user-specific data
      });

      this.profile = await globalThis.avatarClient.getProfile();
      console.log(this.profile.name)
    }
  }
}
```

### Step 3: Add attribute reference and UI elements

Now that we're checking auth status, let's add some Element UI components to our demo scene to display logged-in users' names, and a button to prompt the login process if they are logged out.

Add two entity attributes to the `viverse-manager.mjs` script to reference a "Login" button and a "Logged In As" status label text. Let's also set up a callback on init to call the VIVERSE SDK's `loginWithWorlds()` function on button click, as well.

```javascript
import { Script, Entity } from "playcanvas";

export class ViverseManager extends Script {
  static scriptName = "viverseManager";

  /**
   * @attribute
   * @type {Entity}
   */
  loggedInAsLabelEntity

  /**
   * @attribute
   * @type {Entity}
   */
  loginButton
  
  initialize() {
    this.appId = "gqvuyd5duu"
    this.profile = null
    this.accessToken = null

    this.loginButton.button.on("click", () => { 
      globalThis.viverseClient.loginWithWorlds()  // This will cause a refresh and run the SSO login loop.
    })

    // Call the VIVERSE SDK init logic defined below in this component
    this.loadViverse()
  }
```

Once the script is saved and parsed, add these UI elements in the scene in the PlayCanvas editor and set both attribute references, pointing to the "Logged In As" label's text element and the "Login" button entity.

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

### Step 5: Hook SDK code into UI

With attribute references set, we can now act on these buttons and text elements. We've already set up the button's click callback to run the SSO login redirect if necessary. Let's also change the status message to encourage the user to login in that case.

But if they user is _already_ logged-in (whether from previous VIVERSE experiences, or because they just completed the SSO redirect loop), let's disable the login button, and change its text.

Then, finally, instantiate an `avatarClient` and await the response from its `getProfile()` function to get the user's profile info, including display name and active avatar information.

```javascript
async loadViverse() {
    // Create a new viverseClient instance on globalThis
    globalThis.viverseClient = new globalThis.viverse.client({
      clientId: this.appId,  // 
      domain: 'account.htcvive.com',  // HTC Account domain
    })
    
    // check login status again
    const result = await globalThis.viverseClient.checkAuth()
    
    if (result === undefined) {  // Not logged in
      this.loggedInAsLabelEntity.element.text = "Not logged in! Please log in."
    } else {
      // `result` contains credentials, we're logged in
      
      // Disable the login button and change its child text label
      this.loginButton.button.active = false;
      this.loginButton.children[1].element.text = "Logged In"

      // Since the user is logged in, get the token to start making requests
      this.accessToken = await globalThis.viverseClient.getToken()

      if (this.accessToken == undefined) {
        console.warn("Sanity-check - should have an accessToken at this point.")
      } else {
        // initialize avatar client instance
        globalThis.avatarClient = new globalThis.viverse.avatar({
            baseURL: 'https://sdk-api.viverse.com/',  // VIVERSE API domain
            token: this.accessToken,  // required to query user-specific data
        })

        this.profile = await globalThis.avatarClient.getProfile();
        // Apply name to "Logged In As" label
        this.loggedInAsLabelEntity.element.text = "Logged In As: " + this.profile.name
      }
    }
  }
```

### Step 6: Export and Publish to VIVERSE

That's it! That should fulfil our minimal requirements to log in to VIVERSE using PlayCanvas UI. Now we just need to export and publish.

In PlayCanvas' Publish/Download menus, choose the "DOWNLOAD ZIP," then set your export options and click the first grey "DOWNLOAD," which will prepare you a build, exposing the final orange "DOWNLOAD" button after a few seconds.

<div><figure><img src="../.gitbook/assets/Screenshot 2025-08-07 142330.png" alt=""><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure></div>

Once the build .zip is downloaded, navigate to VIVERSE Studio's Upload section, click "Manage Content" next to the app you created earlier, and use the "Upload Content" section to select the .zip.

From there, you can preview your build, or submit it for content review and approval. This process is explored in great detail in [the VIVERSE Studio section](https://app.gitbook.com/s/4pMiThqqrBzfvP8uy5am/publishing-with-your-viverse-account) of our docs if you have further questions.

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

### Final Build

That's it! The build is ready. Here are the relevant links:

Public PlayCanvas project: [https://playcanvas.com/project/1375569/overview/viverse-auth-sdk--playcanvas](https://playcanvas.com/project/1375569/overview/viverse-auth-sdk--playcanvas)

Demo scene live on VIVERSE: [https://worlds.viverse.com/Rkofb3v](https://worlds.viverse.com/Rkofb3v)

<figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>
