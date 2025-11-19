---
description: >-
  Learn how to combine the VIVERSE Storage SDK with PlayCanvas UI then publish
  the project via VIVERSE Studio
---

# PlayCanvas Storage minimal example

The Storage SDK empowers developers to add persistent data to their games per world for logged-in users. VIVERSE's Storage SDK can be combined with PlayCanvas' powerful Element UI systems to provide an end-to-end solution for saving persistent data in your game. This page will a provide minimum viable example.

<figure><img src="../.gitbook/assets/Screenshot 2025-08-19 175018.png" alt="" width="563"><figcaption></figcaption></figure>

### Pre-requisite #1: Create a World, App ID and Leaderboard in VIVERSE Studio&#x20;

All SDK usage requires an App ID tied to a specific VIVERSE World, which can be created via [VIVERSE Studio](https://studio.viverse.com/upload). This process is described in detail in [our documentation on VIVERSE Studio](https://app.gitbook.com/s/4pMiThqqrBzfvP8uy5am/publishing-with-your-viverse-account).

> _**NOTE:** because VIVERSE SDKs require an App ID, this means VIVERSE SDKs cannot be used with projects published via the PlayCanvas Create SDK extension, which do not have App IDs._

<div><figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/Screenshot 2025-07-31 113911.png" alt=""><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/Screenshot 2025-07-31 115641.png" alt=""><figcaption></figcaption></figure></div>

### Pre-requisite #2: Clone the Login & Authentication SDK example project

We'll base this project on the [PlayCanvas Login & Authentication minimal example](../login-and-authentication-for-the-sdk/playcanvas-login-and-auth-minimal-example.md), which already shows how to add the VIVERSE SDK to external scripts, then `checkAuth()`  and run the SSO login loop if necessary, all within the `viverse-manager.mjs` script. This will be required before we can set up and use the Storage SDK. Fork the project, and don't forget to replace the App ID in the script with the one you created in Pre-requisite #1.

<div><figure><img src="../.gitbook/assets/image (4).png" alt="" width="563"><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/Screenshot 2025-08-19 182239.png" alt=""><figcaption></figcaption></figure></div>

### Step 1: Set Up Additional UI

To start, add a two-column horizontal Layout Group with the login UI as the first child, and a new Group Element as the second child on the right. In that column we can create one UI button and three labels as shown here:

<figure><img src="../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

Then in `viverse-manager.mjs`, add references for a few of these like so:

```javascript
  /**
   * @attribute
   * @type {Entity}
   */
  scoreLabel

  /**
   * @attribute
   * @type {Entity}
   */
  saveScoreButton

  /**
   * @attribute
   * @type {Entity}
   */
  lastSavedScoreLabel
```

### Step 2: Create a Score to Save

Add a `this.score` property to the `initialize()` lifecycle event, and iterate by 1 every frame after a `this.hasCheckedForScore` flag has been set to true (this gives us the chance to check for a value saved in the cloud before we start — we'll come back to this). Now we have  a continually changing value to save to the cloud. This could just as easily be gems, experience points, resources gathered or other gameplay elements, depending on your game's needs.

```javascript
  
  initialize() {
    // somewhere in initialize
    this.score = 0
    this.hasCheckedForScore = false
  }
  
  update() {
    if (this.hasCheckedForScore) {  // wait until this flag is set before updating
      this.score++
      this.scoreLabel.element.text = this.score.toString()
    }
  }
```

### Step 3: Set up the VIVERSE \`storageClient\`

With the code from the Login & Auth example in the `loadViverse()` method, we should have valid credentials to create Storage and Cloud Save SDK clients. The API reference for this process can be found on the Storage SDK page.

```javascript
    // Continued from loadViverse() method, after successful login via checkAuth():
    } else {
      // Get the token to start making requests;
      const accessToken = await globalThis.viverseClient.getToken();

      try {  // Attempt storage client init
          globalThis.storageClient = new globalThis.viverse.storage()
          globalThis.cloudSaveClient = await globalThis.storageClient.newCloudSaveClient(this.appId);
          
          // Start by checking for existing player data
          globalThis.cloudSaveClient.getPlayerData('score', this.accessToken).then((response) => {
            if (response!= null) {  // there exists some data for this property!
              this.score = response
              this.scoreLabel.element.text = this.score.toString()
              this.lastSavedScoreLabel.element.text = "Last saved score: " + this.score.toString()
            } else {  // the response was null - no saved value exists for this key
              this.lastSavedScoreLabel.element.text = "No saved score found. Save now!"
            }
            this.saveScoreButton.button.active = true
            this.hasCheckedForScore = true;  // start iterating on score
          });
        } catch(err) { console.error(err) }
    }
  }
```

### Step 4: Add a Cloud Save Callback

That should function well, but until we submit some cloud data, the `getPlayerData()` check will return null and nothing will display! We already added the "Save Score" button and referenced it with an attribute in our script. Now let's create an set the click callback.

```javascript
  initialize() {
    // somewhere within initialize()
    this.saveScoreButton.button.on("click", () => {
        this.cloudSaveScore();
    });
  }
  
  async cloudSaveScore() {
    let savedScore = await globalThis.cloudSaveClient.setPlayerData("score", this.score.toString(), this.accessToken)
    this.lastSavedScoreLabel.element.text = "Last Saved Score: " + this.score.toString();
  }
```

### Step 5: Export and Publish to VIVERSE

That's it! That should fulfil our minimal requirements to use the VIVERSE Storage SDK along with PlayCanvas UI. Now we just need to export and publish.

In PlayCanvas' Publish/Download menus, choose the "DOWNLOAD ZIP," then set your export options and click the first grey "DOWNLOAD," which will prepare you a build, exposing the final orange "DOWNLOAD" button after a few seconds.

<div><figure><img src="../.gitbook/assets/Screenshot 2025-08-07 142330.png" alt=""><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure></div>

Once the build .zip is downloaded, navigate to VIVERSE Studio's Upload section, click "Manage Content" next to the app you created earlier, and use the "Upload Content" section to select the .zip.

From there, you can preview your build, or submit it for content review and approval. This process is explored in great detail in [the VIVERSE Studio section](https://app.gitbook.com/s/4pMiThqqrBzfvP8uy5am/publishing-with-your-viverse-account) of our docs if you have further questions.

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

### Final Build

The build is ready. Here are the relevant links:

Public PlayCanvas project: [https://playcanvas.com/project/1380461/overview/viverse-storage-sdk--pc](https://playcanvas.com/project/1380461/overview/viverse-storage-sdk--pc)

Demo scene live on VIVERSE: [https://worlds.viverse.com/xESmRTh](https://worlds.viverse.com/xESmRTh)

<figure><img src="../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>
