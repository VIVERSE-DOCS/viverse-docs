---
description: >-
  Learn how to clone a three.js example project then import the VIVERSE SDK to
  add login and authentication features
---

# three.js Login & Auth minimal example

This tutorial demonstrates how to extend a basic three.js example project with VIVERSE SDK functionality, creating an engaging collectible driving game with rapier.js physics.

In this initial tutorial, we'll add the Authentication SDK, as well as several gameplay features that we'll continue to build on in future steps with the Storage and Leaderboard SDK examples.

### Pre-requisite #1: Create a World and App ID in VIVERSE Studio

All SDK usage requires an App ID tied to a specific VIVERSE World, which can be created via [VIVERSE Studio](https://studio.viverse.com/upload). This process is described in detail in [our documentation on VIVERSE Studio](https://app.gitbook.com/s/4pMiThqqrBzfvP8uy5am/publishing-with-your-viverse-account) — but simply create a new app and copy its App ID to get started.

> _**NOTE:** VIVERSE SDKs cannot be used with projects published via the PlayCanvas Create SDK extension, which do not have App IDs._

<figure><img src="../.gitbook/assets/Screenshot 2025-08-07 144325.png" alt="" width="563"><figcaption></figcaption></figure>

### Pre-requisite #2: Clone the three.js Vehicle Controller Example

Start by copying [the official three.js Rapier vehicle controller sample](https://github.com/mrdoob/three.js/blob/master/examples/physics_rapier_vehicle_controller.html) as your foundation. This example provides a complete three.js scene setup with Rapier physics tied to a functional vehicle controller with WASD controls — drive too fast before turning and you can fully flip over, providing a fun challenge driven by Rapier's realistic physics simulation.

<figure><img src="../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

### Step 1: Add VIVERSE SDK Integration

Building on the base three.js vehicle controller example, we'll now add VIVERSE authentication and gameplay enhancements. Simply copy the CDN URL into the `<head>` of your HTML document.

```html
<!-- Add to <head> section: -->
<script src="https://www.viverse.com/static-assets/viverse-sdk/index.umd.cjs"></script>
```

### Step 2: Implement VIVERSE Authentication

#### Client Initialization

This process is described generically in our documentation, [**Login & Authentication for the SDK**](./), but here is how it would apply to a modular three.js script:

```javascript
// Initialize VIVERSE client
function initializeViverseClient() {
  globalThis.viverseClient = new globalThis.viverse.client({
    clientId: "your-app-id", // Replace with your actual App ID from VIVERSE Studio
    domain: "account.htcvive.com",
    cookieDomain: window.location.hostname,
  });
}
```

#### Authentication Check

Once the `viverseClient` is instantiated, use its `checkAuth()` function to determine whether the user is logged in. It will return `undefined` if they are **not** logged-in, or credentials if they are.

```javascript
// Check authentication status
async function checkAuthentication() {
  try {
    const result = await globalThis.viverseClient.checkAuth();

    if (result) {
      isAuthenticated = true;
      userInfo = result;
      updateAuthUI(true, result.account_id);
      console.log("User authenticated:", result);
    } else {
      isAuthenticated = false;
      userInfo = null;
      updateAuthUI(false);
      console.log("User not authenticated");
    }
  } catch (error) {
    console.error("Authentication check failed:", error);
    isAuthenticated = false;
    updateAuthUI(false);
  }
}
```

#### Login Flow

Now that we're checking auth status, let's add some Element UI components to our demo scene to prompt the login process if they are logged out.

```javascript
// Login with VIVERSE Worlds
function loginWithViverse() {
  if (globalThis.viverseClient) {
    globalThis.viverseClient.loginWithWorlds();
  }
}
```

#### Dynamic UI Creation

```javascript
function createAuthUI() {
  // Create authentication status element
  const authElement = document.createElement("div");
  authElement.id = "auth-status";
  authElement.innerHTML = "Checking authentication...";
  document.body.appendChild(authElement);

  // Create login button
  const loginButton = document.createElement("button");
  loginButton.id = "login-button";
  loginButton.innerHTML = "Login with VIVERSE";
  loginButton.addEventListener("click", loginWithViverse);
  document.body.appendChild(loginButton);
}
```

#### UI State Management

```javascript
// Update authentication UI
function updateAuthUI(authenticated, userId = null) {
  const authElement = document.getElementById("auth-status");
  const loginButton = document.getElementById("login-button");

  if (authenticated) {
    authElement.innerHTML = `Logged in as: ${userId}`;
    authElement.className = "authenticated";
    loginButton.style.display = "none";
  } else {
    authElement.innerHTML = "Not logged in";
    authElement.className = "unauthenticated";
    loginButton.style.display = "block";
  }
}
```

Once uploaded and running on VIVERSE, all this authentication functionality should now be working! But what can we do with a logged in VIVERSE user? To find out, we have to add some gameplay features and a scoring system.

### Step 3: Enhance the OrbitControls Camera System

The base vehicle controller example uses a simple static camera setup which points at the car but does not follow it closely. We'll upgrade this with smart car following behavior for a more engaging experience that will still allow the user to view the vehicle and scene from a variety of angles.

#### Default Settings + Events for Camera Following

```javascript
// Initialize OrbitControls for zoom and rotation
controls = new OrbitControls(camera, renderer.domElement);
controls.target = new THREE.Vector3(0, 2, 0);
controls.enablePan = false; // Disable panning, keep zoom and rotate
controls.minDistance = 3; // Minimum zoom distance
controls.maxDistance = 30; // Maximum zoom distance
controls.maxPolarAngle = Math.PI * 0.8; // Prevent camera from going too low
controls.enableDamping = true; // Enable damping for smoother feel
controls.dampingFactor = 0.1;

// Track when user interaction ends to update follow offset
window.userControlling = false;

controls.addEventListener("start", () => {
  window.userControlling = true;
});

controls.addEventListener("end", () => {
  window.userControlling = false;
  // Update the follow offset when user stops controlling
  updateCameraOffset();
});
```

#### Camera Updates

```javascript
function updateCamera() {
  if (!car || !controls) return;

  // Get car's world position and rotation
  const carPosition = car.position.clone();
  const carQuaternion = car.quaternion.clone();

  // Create a level quaternion (only Y rotation, no roll/pitch)
  const carEuler = new THREE.Euler().setFromQuaternion(carQuaternion, "YXZ");
  const levelQuaternion = new THREE.Quaternion().setFromEuler(
    new THREE.Euler(0, carEuler.y, 0, "YXZ")
  );

  if (!window.userControlling) {
    // When user is not controlling, use the stored offset for following
    const desiredCameraPosition = cameraOffset.clone();
    desiredCameraPosition.applyQuaternion(levelQuaternion); // Use level quaternion
    desiredCameraPosition.add(carPosition);

    // Ensure camera stays above ground (minimum height)
    const minCameraHeight = 1.0; // Minimum height above ground
    if (desiredCameraPosition.y < minCameraHeight) {
      desiredCameraPosition.y = minCameraHeight;
    }

    // Smoothly move camera and target to follow the car
    const lerpFactor = 0.05;
    controls.target.lerp(carPosition, lerpFactor);
    camera.position.lerp(desiredCameraPosition, lerpFactor);
  } else {
    // When user is controlling, just update target to follow car position
    const targetLerpFactor = 0.05;
    controls.target.lerp(carPosition, targetLerpFactor);

    // Also ensure user-controlled camera doesn't go below ground
    if (camera.position.y < 1.0) {
      camera.position.y = 1.0;
    }
  }

  // Update controls
  controls.update();
}
```

### Step 5: Add Game Mechanics - Collectibles System

Now we'll add a scoring system with collectible items to transform the basic driving demo into an engaging game. This will also give us a score to track with the Leaderboard and Storage SDKs in future tutorials.

#### Collectible Creation

```javascript
function createCollectible(x, y, z) {
  const geometry = new THREE.OctahedronGeometry(0.5);
  const material = new THREE.MeshStandardMaterial({
    color: 0xffd700,
    emissive: 0x444400,
    metalness: 0.3,
    roughness: 0.1,
  });
  const mesh = new THREE.Mesh(geometry, material);

  mesh.position.set(x, y, z);
  mesh.castShadow = true;
  mesh.receiveShadow = true;

  // Add physics body for collision detection
  physics.addMesh(mesh, 0); // Mass 0 for static/kinematic body
  mesh.userData.isCollectible = true;
  mesh.userData.collected = false;
  mesh.userData.originalY = y;
  mesh.userData.time = Math.random() * Math.PI * 2; // Random phase for animation

  scene.add(mesh);
  collectibles.push(mesh);

  return mesh;
}
```

#### Collectible Spawning System

```javascript
function createCollectibles() {
  const numCollectibles = 15; // Number of collectibles to spawn
  const groundSize = 40; // Size of the ground area (matches the ground geometry)
  const minDistance = 3; // Minimum distance between collectibles
  const carStartArea = 5; // Radius around car start position to avoid

  const spawnedPositions = [];

  for (let i = 0; i < numCollectibles; i++) {
    let position;
    let attempts = 0;
    const maxAttempts = 50;

    do {
      // Generate random position within ground bounds
      position = {
        x: (Math.random() - 0.5) * (groundSize - 4), // Leave some margin from edges
        y: 1,
        z: (Math.random() - 0.5) * (groundSize - 4) - 20, // Offset by ground position
      };
      attempts++;
    } while (
      attempts < maxAttempts &&
      (isPositionTooClose(position, spawnedPositions, minDistance) ||
        isPositionTooClose(position, [{ x: 0, z: 0 }], carStartArea))
    );

    // If we found a valid position, spawn the collectible
    if (attempts < maxAttempts) {
      createCollectible(position.x, position.y, position.z);
      spawnedPositions.push(position);
    }
  }

  updateScoreUI();
}

function isPositionTooClose(newPos, existingPositions, minDistance) {
  return existingPositions.some((pos) => {
    const dx = newPos.x - pos.x;
    const dz = newPos.z - pos.z;
    return Math.sqrt(dx * dx + dz * dz) < minDistance;
  });
}
```

#### Collision Detection and Scoring

For this demo, a simple distance-based collision check should suffice.

```javascript
function checkCollisions() {
  if (!car || !chassis) return;

  const carPosition = car.position;
  const collectDistance = 1.5; // Distance threshold for collection

  const collectiblesToCheck = [...collectibles];

  collectiblesToCheck.forEach((collectible) => {
    if (!collectible || collectible.userData.collected || !collectible.visible)
      return;

    const distance = carPosition.distanceTo(collectible.position);
    if (distance < collectDistance) {
      collectPickup(collectible);
    }
  });
}

function collectPickup(collectible) {
  if (collectible.userData.collected) return;

  collectible.userData.collected = true;
  score += 100; // Add 100 points per collectible

  // Immediately hide the collectible
  collectible.visible = false;

  // Remove from physics world immediately
  if (collectible.userData.physics) {
    physics.removeBody(collectible.userData.physics.body);
    collectible.userData.physics = null; // Clear the reference
  }

  // Remove from collectibles array immediately
  const index = collectibles.indexOf(collectible);
  if (index > -1) {
    collectibles.splice(index, 1);
  }

  // Remove from scene immediately
  scene.remove(collectible);

  // Clean up geometry and material
  if (collectible.geometry) {
    collectible.geometry.dispose();
  }
  if (collectible.material) {
    collectible.material.dispose();
  }

  updateScoreUI();

  // Spawn a new collectible to replace the collected one
  spawnNewCollectible();
}

function spawnNewCollectible() {
  const groundSize = 40;
  const minDistance = 3;
  const carStartArea = 5;
  const maxAttempts = 50;

  let position;
  let attempts = 0;

  do {
    // Generate random position within ground bounds
    position = {
      x: (Math.random() - 0.5) * (groundSize - 4),
      y: 1,
      z: (Math.random() - 0.5) * (groundSize - 4) - 20,
    };
    attempts++;
  } while (
    attempts < maxAttempts &&
    (isPositionTooClose(
      position,
      collectibles.map((c) => c.position),
      minDistance
    ) ||
      isPositionTooClose(position, [{ x: 0, z: 0 }], carStartArea))
  );

  // If we found a valid position, spawn the collectible
  if (attempts < maxAttempts) {
    createCollectible(position.x, position.y, position.z);
  }
}
```

### Step 6: Integration and Initialization

Building on the existing three.js vehicle controller, add these integrations:

```javascript
// 1. Add VIVERSE authentication variables at the top
let isAuthenticated = false;
let userInfo = null;

// 2. Add score tracking variables
let score = 0;
let collectibles = [];

// 3. In your existing init() function, add these calls:
async function init() {
  // ... existing three.js setup from base example ...

  // NEW: Create authentication UI elements
  createAuthUI();

  // NEW: Initialize VIVERSE authentication after physics
  await initPhysics(); // existing
  initializeViverseClient(); // NEW
  checkAuthentication(); // NEW

  // NEW: Set up enhanced camera controls (replaces basic camera)
  setupEnhancedCamera();

  // ... rest of existing initialization ...
}

function animate() {
  if (vehicleController) {
    updateCarControl();
    vehicleController.updateVehicle(1 / 60);
    updateWheels(); // Update wheel positions and rotations
  }

  // Update camera to follow car
  updateCamera();

  // Update collectibles animations and check collisions
  animateCollectibles();
  checkCollisions();

  if (physicsHelper) physicsHelper.update();

  renderer.render(scene, camera);
}
```

### Step 6: Build and Publish to VIVERSE

That's it! That should fulfil our minimal requirements create a small mini game we can publish to VIVERSE to then run login and authentication check features. Now we just need to build and publish.

Whatever your build process, simply zip the final build folder to prepare to upload. In the demo project we're using Vite.js, so just run `npm run build` to engage that. This will create all necessary static build files in the `/dist` folder of your project.

<figure><img src="../.gitbook/assets/image (26).png" alt="" width="563"><figcaption></figcaption></figure>

Once the build .zip is ready, navigate to VIVERSE Studio's Upload section, click "Manage Content" next to the app you created earlier, and use the "Upload Content" section to select the .zip.

From there, you can preview your build, or submit it for content review and approval. This process is explored in great detail in [the VIVERSE Studio section](https://app.gitbook.com/s/4pMiThqqrBzfvP8uy5am/publishing-with-your-viverse-account) of our docs if you have further questions.

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

### Final Build

That's it! The build is ready. Here are the relevant links:&#x20;

three.js example project source code:&#x20;

{% file src="../.gitbook/assets/vv-auth-three.zip" %}

Demo scene live on VIVERSE: [https://worlds.viverse.com/DQC9Lpd](https://worlds.viverse.com/DQC9Lpd)
