---
description: >-
  Learn how to extend our three.js driving project with the VIVERSE Storage SDK
  to add persistent data
---

# three.js Storage minimal example

This guide extends the official [VIVERSE three.js Login & Auth minimal example](https://docs.viverse.com/developer-tools/login-and-authentication-for-the-sdk/three.js-login-and-auth-minimal-example) by adding persistent data storage functionality using the VIVERSE Storage SDK. This can be used to track any number of gameplay elements, from experience points, to resources, to equippable items.

### Pre-requisite:

Before starting this guide, you should have completed the [VIVERSE three.js Login & Auth minimal example](https://docs.viverse.com/developer-tools/login-and-authentication-for-the-sdk/three.js-login-and-auth-minimal-example) which includes features such as:

1. ✅ A working three.js vehicle controller with VIVERSE authentication
2. ✅ A VIVERSE Studio App ID
3. ✅ A basic collectible driving game featuring rapier.js physics

### Step 1: Add Storage SDK Variables

Add these variables alongside your existing authentication variables:

```javascript
// VIVERSE Authentication variables (existing)
let isAuthenticated = false;
let userInfo = null;

// ADD THESE NEW VARIABLES:
let accessToken = null;

// VIVERSE Storage variables
let storageClient = null;
let cloudSaveClient = null;
```

### Step 2: Update VIVERSE Client Initialization

Modify your existing `initializeViverseClient()` function to also initialize the storage client:

```javascript
// Initialize VIVERSE client
function initializeViverseClient() {
  globalThis.viverseClient = new globalThis.viverse.client({
    clientId: "your-app-id", // Replace with your actual App ID
    domain: "account.htcvive.com",
    cookieDomain: window.location.hostname,
  });

  // ADD THIS: Initialize storage client
  initializeStorageClient();
}

// ADD THIS NEW FUNCTION:
// Initialize VIVERSE Storage client
async function initializeStorageClient() {
  try {
    storageClient = new globalThis.viverse.storage();
    console.log("Storage client initialized");
  } catch (error) {
    console.error("Failed to initialize storage client:", error);
  }
}
```

### Step 3: Update Authentication Check

Modify your existing `checkAuthentication()` function to capture the access token and initialize cloud save:

```javascript
// Check authentication status
async function checkAuthentication() {
  try {
    const result = await globalThis.viverseClient.checkAuth();

    if (result) {
      isAuthenticated = true;
      userInfo = result;
      accessToken = result.access_token; // ADD THIS LINE
      updateAuthUI(true, result.account_id);
      console.log("User authenticated:", result);

      // ADD THESE LINES:
      // Initialize cloud save client after authentication
      await initializeCloudSaveClient();
    }
}

// Initialize Cloud Save client
async function initializeCloudSaveClient() {
  if (!storageClient || !accessToken) {
    console.log("Storage client or access token not available");
    return;
  }

  try {
    cloudSaveClient = await storageClient.newCloudSaveClient("your-app-id"); // Use same App ID
    console.log("Cloud save client initialized");
  } catch (error) {
    console.error("Failed to initialize cloud save client:", error);
  }
}
```

### Step 4: Add Save Score Function

Now that the client has been initialized, and a score checked for, let's add a save score function:

```javascript
// Save current score to cloud storage
async function saveScore() {
  if (!cloudSaveClient || !accessToken || !isAuthenticated) {
    console.log(
      "Cannot save score: not authenticated or storage not initialized"
    );
    updateSaveStatus("Not authenticated", false);
    return false;
  }

  try {
    updateSaveStatus("Saving...", null);

    const scoreData = {
      score: score,
      timestamp: new Date().toISOString(),
      gameVersion: "1.0",
    };

    // There is currently a bug where this returns undefined immediately
    const result = await cloudSaveClient.setPlayerData(
      "highScore",
      scoreData,
      accessToken
    );

    console.log("Save API response:", result);
    console.log("Score saved successfully:", scoreData);
    updateSaveStatus("Score saved!", true);
    return true;
  } catch (error) {
    console.error("Error saving score:", error);
    updateSaveStatus("Save error", false);
    return false;
  }
}
```

### Step 5: Add Load Score Function

Add this function after the save function:

```javascript
// Load saved score from cloud storage
async function loadScore() {
  if (!cloudSaveClient || !accessToken || !isAuthenticated) {
    console.log(
      "Cannot load score: not authenticated or storage not initialized"
    );
    return null;
  }

  try {
    updateSaveStatus("Loading...", null);

    const savedData = await cloudSaveClient.getPlayerData(
      "highScore",
      accessToken
    );

    if (savedData && savedData.score !== undefined) {
      console.log("Score loaded successfully:", savedData);

      // Only update score if saved score is higher than current
      if (savedData.score > score) {
        score = savedData.score;
        updateScoreUI();
        updateSaveStatus(`Loaded score: ${savedData.score}`, true);
      } else {
        updateSaveStatus("Score loaded", true);
      }

      return savedData;
    } else {
      console.log("No saved score found");
      updateSaveStatus("No saved score", null);
      return null;
    }
  } catch (error) {
    console.error("Error loading score:", error);
    updateSaveStatus("Load error", false);
    return null;
  }
}
```

### Step 6: Add Save Status UI Function

Add this function to handle UI status updates:

```javascript
// Update save status UI
function updateSaveStatus(message, success) {
  const statusElement = document.getElementById("save-status");
  if (statusElement) {
    statusElement.textContent = message;
    statusElement.className =
      success === true
        ? "save-success"
        : success === false
        ? "save-error"
        : "save-neutral";
  }
}

// Make functions available globally for onclick handlers
window.saveScore = saveScore;
window.loadScore = loadScore;
```

### Step 7: Update Score UI with Save/Load Buttons

Replace your existing score UI div with this enhanced version:

```html
<div id="score-ui">
  <div>Score: <span id="score-value">0</span></div>
  <div id="save-status"></div>
  <div>
    <button
      id="save-button"
      onclick="saveScore()">
      Save Score
    </button>
    <button
      id="load-button"
      onclick="loadScore()">
      Load Score
    </button>
  </div>
</div>
```

### Step 8: Add Auto-Save to Collectible Pickup

Modify your existing `collectPickup()` function to automatically save when score increases:

```javascript
function collectPickup(collectible) {
  if (collectible.userData.collected) return;

  collectible.userData.collected = true;
  const oldScore = score;
  score += 100; // Add 100 points per collectible

  // ... existing cleanup code for collectible ...

  updateScoreUI();

  // ADD THIS BLOCK:
  // Auto-save score if authenticated (don't await to avoid blocking gameplay)
  if (isAuthenticated && score > oldScore) {
    console.log("Attempting auto-save...");
    saveScore().catch((error) => {
      console.error("Auto-save failed:", error);
    });
  }

  // Spawn a new collectible to replace the collected one
  spawnNewCollectible();
}
```

### Features Added

✅ **Persistent Score Storage**: Scores are automatically saved to VIVERSE cloud storage\
✅ **Auto-Save**: Score automatically saves when collecting items and before reset\
✅ **Manual Save/Load**: Players can manually save and load scores using buttons\
✅ **High Score Protection**: Only loads saved scores if they're higher than current score\
✅ **Visual Feedback**: Clear status messages show save/load operations\
✅ **Error Handling**: Graceful error handling with user feedback\
✅ **Non-blocking Operations**: Save operations don't interrupt gameplay

### Step 9: Build and Publish to VIVERSE

That's it! That should fulfil our minimal requirements create a small mini game and track persistent scores for logged in users over time. Now we just need to build and publish.

Whatever your build process, simply zip the final build folder to prepare to upload. In the demo project we're using Vite.js, so just run `npm run build` to engage that. This will create all necessary static build files in the `/dist` folder of your project.

<figure><img src="../.gitbook/assets/image (26).png" alt="" width="563"><figcaption></figcaption></figure>

Once the build .zip is ready, navigate to VIVERSE Studio's Upload section, click "Manage Content" next to the app you created earlier, and use the "Upload Content" section to select the .zip.

From there, you can preview your build, or submit it for content review and approval. This process is explored in great detail in [the VIVERSE Studio section](https://app.gitbook.com/s/4pMiThqqrBzfvP8uy5am/publishing-with-your-viverse-account) of our docs if you have further questions.

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

### Final Build

That's it! The build is ready. Here are the relevant links:&#x20;

three.js example project source code:&#x20;

{% file src="../.gitbook/assets/vv-storage-three.zip" %}

Demo scene live on VIVERSE: [https://worlds.viverse.com/uSmtGgV](https://worlds.viverse.com/uSmtGgV)
