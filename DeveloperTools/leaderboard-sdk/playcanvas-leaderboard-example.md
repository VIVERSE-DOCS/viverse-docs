---
description: >-
  Learn how to combine the VIVERSE Leaderboard SDK with PlayCanvas UI then
  publish the project via VIVERSE Studio
hidden: true
---

# PlayCanvas Leaderboard example

Leaderboards allow players to compare their performance against others, increasing engagement time and replayability. VIVERSE's Leaderboard SDK can be combined with PlayCanvas' powerful screen- and world-space Element UI systems to provide an end-to-end solution for this in your game.

<figure><img src="../.gitbook/assets/image (11).png" alt="" width="563"><figcaption></figcaption></figure>

### Pre-requisite: Create a World, App ID and Leaderboard in VIVERSE Studio&#x20;

All SDK usage requires an App ID tied to a specific VIVERSE World, which can be created via [VIVERSE Studio](https://studio.viverse.com/upload). This process is described in detail in [our documentation on VIVERSE Studio](https://app.gitbook.com/s/4pMiThqqrBzfvP8uy5am/publishing-with-your-viverse-account).

> _**NOTE:** because VIVERSE SDKs require an App ID, this means VIVERSE SDKs cannot be used with projects published via the PlayCanvas Create SDK extension, which do not have App IDs._

<div><figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/Screenshot 2025-07-31 113911.png" alt=""><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/Screenshot 2025-07-31 115641.png" alt=""><figcaption></figcaption></figure></div>

### Step 0: PlayCanvas Scrolling UI tutorial

PlayCanvas already maintains many great tutorials for their engine, including a scrolling UI system with dynamic sizing. We'll click the 🍴 Fork button to clone this project to use [as a starting point](https://app.gitbook.com/s/d69no6CEQxke8g3u7ZrP/) and ensure our UI practices are standard for the engine. This already has a few scripts for adding UI entries to a list based on a PlayCanvas template.

<figure><img src="../.gitbook/assets/image (18).png" alt="" width="375"><figcaption></figcaption></figure>

### Step 1: Add the VIVERSE SDK as an external script

Once the project is forked, go to the new project Settings in the PlayCanvas editor, and in the EXTERNAL SCRIPTS menu, add one URL entry, and point to \`[`https://www.viverse.com/static-assets/viverse-sdk/1.2.9/viverse-sdk.umd.js`](https://www.viverse.com/static-assets/viverse-sdk/1.2.9/viverse-sdk.umd.js)\` as in this screenshot. This will ensure the VIVERSE SDK is loaded first and that your PlayCanvas logic has full access to its global methods.

<figure><img src="../.gitbook/assets/image (15).png" alt="" width="153"><figcaption></figcaption></figure>

### Step 2: Login and Auth

The player must be logged into VIVERSE to either request leaderboard data or to submit it. So this example first checks if the user is logged in, and if not, forces login through VIVERSE's "single sign-on" (SSO) redirect loop.

We'll add a new script called `leaderboard.mjs` to manage the VIVERSE SDK services and handle authentication. These code comments also explain the significance of each line, but this process is described in detail in our documentation, [**Login & Authentication for the SDK**](../login-and-authentication-for-the-sdk/), as well.

```javascript
import { Script, Entity } from "playcanvas";

export class Leaderboard extends Script {
  static scriptName = "leaderboard";

  initialize() {
    // Log globalThis for reference - the VIVERSE SDK relies on it
    console.log("globalThis", globalThis);

    // Call the VIVERSE SDK init logic defined below in this component
    this.loadViverse();
  }
  
  async loadViverse() {
    // Create a new viverseClient instance on globalThis
    globalThis.viverseClient = new globalThis.viverse.client({
      clientId: '9dbensqh9g', // the App ID from VIVERSE Studio described above
      domain: 'account.htcvive.com', // HTC Account domain
    });
    
    // check login status again
    const result = await globalThis.viverseClient.checkAuth();
    
    if (result === undefined) { // undefined means not logged in
      // This will cause a refresh and forward to the VIVERSE login process
      globalThis.viverseClient.loginWithWorlds();
    } else {  // `result` contains credentials to make authorized requests 
      console.log("auth result!", result)
    }
  }
}
```

### Step 3: Set up the VIVERSE \`gameDashboardClient\`

Now we should have valid credentials to make requests to the Leaderboard SDK. Let's add the&#x20;

```javascript

      // Continued from above
      console.log("auth result!", result) // contains credentials if not undefined

      // If the user is logged in, get the token to start making requests;
      const accessToken = await globalThis.viverseClient.getToken();

      // Start the leaderboard init process by creasting a gameDashboardClient instance
      globalThis.gameDashboardClient = new globalThis.viverse.gameDashboard({
        baseURL: 'https://www.viveport.com/',
        communityBaseURL: 'https://www.viverse.com/',
        token: accessToken
      });

      // Immediately fetch high scores if logged in and gameDashboardClient was created
      this.getHighScores();
    }
  }

  async getHighScores() {
    // A valid leaderboard config example:
    const config = {
        name: "highScores",  // should match the name set in SDK Settings menu
        range_start: 0,
        range_end: 100,
        region: "global",
        time_range: "alltime",
        around_user: false
    };

    // Again the App ID is required
    const leaderboardResponse =
      await globalThis.gameDashboardClient.getLeaderboard('9dbensqh9g', config);
  }
```

### Step 4: Tie Leaderboard Response to PlayCanvas UI elements

Now we
