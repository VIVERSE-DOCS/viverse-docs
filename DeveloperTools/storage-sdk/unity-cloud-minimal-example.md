---
description: >-
  Build a minimal Unity demo showcasing all VIVERSE Cloud SDK features. Includes
  simplified UI setup and complete testing workflow for Cloud Save and UserApp
  APIs.
hidden: true
---

# Unity Cloud Minimal Example

***

### &#x20;

### Overview

Build a minimal Unity demo showcasing all VIVERSE Cloud SDK features. Includes simplified UI setup and complete testing workflow for Cloud Save and UserApp APIs.

### Prerequisites

* Unity 2021 LTS or newer (install the WebGL module if you plan to target WebGL)
* App ID from VIVERSE Studio (https://worlds.viverse.com/)
* VIVERSE login/auth unitypackage (e.g., SDK\_v0.92 1.unitypackage)
* User must be logged in via LoginManager before using Cloud SDK features

## Step 1. Import the VIVERSE package

{% stepper %}
{% step %}
### Open Unity

Create new project within Unity or use existing project with LoginManager already set up.
{% endstep %}

{% step %}
### Import VIVERSE Unity Package

Assets → Import Package → Custom Package…, select the VIVERSE unity package, click **Import**.
{% endstep %}

{% step %}
### Review Added Scripts

Scripts such as `LoginManager.cs`, `CloudSaveService.cs`, `HttpServer.cs`, and `ViverseSDK 1.jslib` are now available—no code edits needed.
{% endstep %}
{% endstepper %}

## Step 2. Setup Login (Required for Cloud SDK)

{% stepper %}
{% step %}
### Setup Login First

The Cloud SDK requires authentication. Follow the Login & Authentication guide to set up:

* LoginManager component
* Login UI with Login button
* App ID configuration
* HttpServer component (for Editor/Windows testing)

Ensure you can successfully log in before proceeding to Cloud SDK setup.
{% endstep %}
{% endstepper %}

## Step 3. Build the Cloud SDK UI

{% stepper %}
{% step %}
### Setup Canvas

A. GameObject → UI → Canvas (name it CloudSaveCanvas).

B. Leave the auto-created EventSystem in the scene.

C. Configure CloudSaveCanvas:

* Render Mode: Screen Space - Overlay
* Canvas Scaler → UI Scale Mode: Scale With Screen Size, Reference Resolution: 1920×1080
{% endstep %}

{% step %}
### Create Main Container

A. Right-click CloudSaveCanvas → Create Empty → rename to MainContainer

B. Add Component → Layout → Vertical Layout Group

C. Set Vertical Layout Group properties:

* Child Alignment: Upper Left
* Padding: Left = 20, Right = 20, Top = 20, Bottom = 60
* Spacing: 30
* Child Force Expand: Width = true, Height = false

D. In RectTransform, set Anchor Presets to stretch-stretch (hold Alt+Shift), then set all margins to 0
{% endstep %}

{% step %}
### Create Panels Structure

A. **Create Status Bar Container:**

* Right-click MainContainer → Create Empty → rename to StatusBar
* Add Component → Layout → Horizontal Layout Group
* Set Horizontal Layout Group properties:
  * Child Alignment: Middle Left
  * Padding: Left = 20, Right = 20, Top = 10, Bottom = 10
  * Spacing: 0
* In RectTransform, set Height = 40
* Add Component → Layout → Layout Element
* Set Layout Element: Preferred Height = 40, Flexible Height = 0, Flexible Width = 1

B. **Create Left Panel (Cloud Save Section):**

* Right-click MainContainer → Create Empty → rename to CloudSavePanel
* Add Component → Layout → Vertical Layout Group
* Set Vertical Layout Group properties:
  * Child Alignment: Upper Left
  * Padding: All = 15
  * Spacing: 10
  * Child Force Expand: Width = true, Height = false
* In RectTransform, set Width = 450, Height = 400
* Add Component → Layout → Layout Element
* Set Layout Element: Preferred Width = 450, Preferred Height = 400

C. **Create Right Panel (UserApp Section):**

* Right-click MainContainer → Create Empty → rename to UserAppPanel
* Add Component → Layout → Vertical Layout Group
* Set Vertical Layout Group properties:
  * Child Alignment: Upper Left
  * Padding: All = 15
  * Spacing: 10
  * Child Force Expand: Width = true, Height = false
* In RectTransform, set Width = 450, Height = 400
* Add Component → Layout → Layout Element
* Set Layout Element: Preferred Width = 450, Preferred Height = 400

D. **Create Horizontal Container:**

* Right-click MainContainer → Create Empty → rename to ContentRow
* Add Component → Layout → Horizontal Layout Group
* Set Horizontal Layout Group properties:
  * Child Alignment: Upper Left
  * Padding: All = 0
  * Spacing: 30
  * Child Force Expand: Width = false, Height = false
* Move CloudSavePanel and UserAppPanel to be children of ContentRow (drag in Hierarchy)
{% endstep %}

{% step %}
### Create Cloud Save Section UI

A. **Create Section Header:**

* Right-click CloudSavePanel → UI → Text - TextMeshPro → rename to CloudSaveHeader
* Text: "Cloud Save API"
* Font Size: 24, Font Style: Bold
* Alignment: Center
* In RectTransform, set Height = 35

B. **Create Buttons:**

* Right-click CloudSavePanel → UI → Button (TextMeshPro) → rename to LoadButton
  * Change button text to "Load Cloud Save"
  * Font Size: 16
  * In the button's RectTransform, set Height = 40
* Right-click CloudSavePanel → UI → Button (TextMeshPro) → rename to SaveButton
  * Change button text to "Save to Cloud"
  * Font Size: 16
  * In the button's RectTransform, set Height = 40

C. **Create Data Display:**

* Right-click CloudSavePanel → UI → Text - TextMeshPro → rename to CloudSaveDataText
* Text: "No data loaded"
* Font Size: 12
* Alignment: Left
* In RectTransform, set Width = 420, Height = 250
* Set Text Wrapping Mode to Normal
* Add Component → Layout → Layout Element
* Set Layout Element: Preferred Height = 250, Flexible Height = 1
{% endstep %}

{% step %}
### Create UserApp Section UI

A. **Create Section Header:**

* Right-click UserAppPanel → UI → Text - TextMeshPro → rename to UserAppHeader
* Text: "UserApp API"
* Font Size: 24, Font Style: Bold
* Alignment: Center
* In RectTransform, set Height = 35

B. **Create Buttons:**

* Right-click UserAppPanel → UI → Button (TextMeshPro) → rename to GetLatestButton
  * Change button text to "Get Latest"
  * Font Size: 16
  * In the button's RectTransform, set Height = 40
* Right-click UserAppPanel → UI → Button (TextMeshPro) → rename to SaveUserAppButton
  * Change button text to "Save User Data"
  * Font Size: 16
  * In the button's RectTransform, set Height = 40
* Right-click UserAppPanel → UI → Button (TextMeshPro) → rename to GetAllButton
  * Change button text to "Get All Records"
  * Font Size: 16
  * In the button's RectTransform, set Height = 40
* Right-click UserAppPanel → UI → Button (TextMeshPro) → rename to DeleteButton
  * Change button text to "Delete Version"
  * Font Size: 16
  * In the button's RectTransform, set Height = 40

C. **Create Input Fields Container:**

* Right-click UserAppPanel → Create Empty → rename to InputFieldsContainer
* Add Component → Layout → Vertical Layout Group
* Set Vertical Layout Group properties:
  * Spacing: 8
  * Padding: Top = 5, Bottom = 5
  * Child Force Expand: Width = true, Height = false

D. **Create Input Fields:**

* Right-click InputFieldsContainer → UI → Input Field - TextMeshPro → rename to LevelInput
  * Placeholder text: "Level"
  * Font Size: 14
  * In the input field's RectTransform, set Height = 35
* Right-click InputFieldsContainer → UI → Input Field - TextMeshPro → rename to ScoreInput
  * Placeholder text: "Score"
  * Font Size: 14
  * In the input field's RectTransform, set Height = 35
* Right-click InputFieldsContainer → UI → Input Field - TextMeshPro → rename to VersionInput
  * Placeholder text: "Version (for delete)"
  * Font Size: 14
  * In the input field's RectTransform, set Height = 35

E. **Create Data Display:**

* Right-click UserAppPanel → UI → Text - TextMeshPro → rename to UserAppDataText
* Text: "No data loaded"
* Font Size: 12
* Alignment: Left
* In RectTransform, set Width = 420, Height = 200
* Set Text Wrapping Mode to Normal
* Add Component → Layout → Layout Element
* Set Layout Element: Preferred Height = 200, Flexible Height = 1
{% endstep %}

{% step %}
### Create Status Bar

A. Right-click StatusBar → UI → Text - TextMeshPro → rename to StatusText

B. Text: "Status: Ready - Please login first"

C. Font Size: 14

D. Alignment: Center

E. In the text's RectTransform, set Width = 800, Height = 30

F. Add Component → Layout → Layout Element

G. Set Layout Element: Preferred Width = 800, Flexible Width = 1
{% endstep %}

{% step %}
### Verify UI Setup

A. **Check Hierarchy Structure:** Your Hierarchy should look like:

```
CloudSaveCanvas
├── EventSystem
└── MainContainer
    ├── StatusBar
    │   └── StatusText
    └── ContentRow
        ├── CloudSavePanel
        │   ├── CloudSaveHeader
        │   ├── LoadButton
        │   ├── SaveButton
        │   └── CloudSaveDataText
        └── UserAppPanel
            ├── UserAppHeader
            ├── GetLatestButton
            ├── SaveUserAppButton
            ├── GetAllButton
            ├── DeleteButton
            ├── InputFieldsContainer
            │   ├── LevelInput
            │   ├── ScoreInput
            │   └── VersionInput
            └── UserAppDataText
```

B. **Test Layout:**

* Press **Play** to see your UI in the Game view
* Verify all elements are visible and properly organized
* Check that panels are side-by-side with proper spacing
* Verify status bar is at the top

C. **Fine-tune if needed:**

* Adjust spacing in Layout Groups if elements are too close/far
* Modify panel widths if content doesn't fit
* Adjust font sizes for better readability
* Use RectTransform anchors for responsive positioning

D. **Stop Play mode** before continuing to next step.
{% endstep %}
{% endstepper %}

## Step 4. Add the Cloud SDK controller script

{% stepper %}
{% step %}
### Create CloudSaveController GameObject

Under CloudSaveCanvas, create an empty GameObject named CloudSaveController.
{% endstep %}

{% step %}
### Add CloudSaveUIController Script

Add this script to CloudSaveController:

```csharp
using UnityEngine;
using UnityEngine.UI;
using TMPro;
using ViverseSDK.Login;
using ViverseSDK.CloudSave;
using System.Collections.Generic;

/// <summary>
/// Handles the UI layer for VIVERSE Cloud SDK.
/// Demonstrates all Cloud SDK features: Cloud Save API and UserApp API.
/// </summary>
[RequireComponent(typeof(CloudSaveService))]
public class CloudSaveUIController : MonoBehaviour
{
    [Header("App Settings")]
    [SerializeField]
    private string appId;         // Your VIVERSE App ID (assign in Inspector)

    [Header("UI References - Cloud Save API")]
    [SerializeField] private Button loadButton;
    [SerializeField] private Button saveButton;
    [SerializeField] private TMP_Text cloudSaveDataText;

    [Header("UI References - UserApp API")]
    [SerializeField] private Button getLatestButton;
    [SerializeField] private Button saveUserAppButton;
    [SerializeField] private Button getAllButton;
    [SerializeField] private Button deleteButton;
    [SerializeField] private TMP_InputField levelInput;
    [SerializeField] private TMP_InputField scoreInput;
    [SerializeField] private TMP_InputField versionInput;
    [SerializeField] private TMP_Text userAppDataText;

    [Header("Status")]
    [SerializeField] private TMP_Text statusText;

    [Header("Sample Data")]
    [SerializeField] private int sampleLevel = 12;
    [SerializeField] private string sampleClass = "warrior";
    [SerializeField] private List<string> sampleInventory = new List<string> { "sword", "shield", "potion" };
    [SerializeField] private int sampleScore = 600;

    private CloudSaveService cloudSaveService;
    private LoginManager loginManager;

    // Current loaded data
    private CloudSaveService.CloudSaveResponseWrapper currentCloudData;
    private CloudSaveService.UserAppLatestResponse currentLatestData;
    private List<CloudSaveService.UserAppData> currentAllData = new List<CloudSaveService.UserAppData>();

    private void Awake()
    {
        // Get required components
        cloudSaveService = GetComponent<CloudSaveService>();
        loginManager = FindObjectOfType<LoginManager>();

        if (loginManager == null)
        {
            Debug.LogError("[CloudSaveUI] LoginManager not found! Cloud SDK requires login.");
        }

        // Wire up button handlers
        SetupButtons();
    }

    private void Start()
    {
        // Subscribe to CloudSaveService events
        SubscribeToEvents();

        // Subscribe to LoginManager events
        if (loginManager != null)
        {
            loginManager.OnLoginStateChanged += OnLoginStateChanged;
        }

        // Initialize UI state
        UpdateStatus("Ready - Please login first");
        UpdateButtonStates(false);
    }

    private void SetupButtons()
    {
        // Cloud Save API buttons
        if (loadButton != null)
            loadButton.onClick.AddListener(OnLoadCloudSave);
        if (saveButton != null)
            saveButton.onClick.AddListener(OnSaveCloudSave);

        // UserApp API buttons
        if (getLatestButton != null)
            getLatestButton.onClick.AddListener(OnGetLatest);
        if (saveUserAppButton != null)
            saveUserAppButton.onClick.AddListener(OnSaveUserApp);
        if (getAllButton != null)
            getAllButton.onClick.AddListener(OnGetAll);
        if (deleteButton != null)
            deleteButton.onClick.AddListener(OnDeleteVersion);
    }

    private void SubscribeToEvents()
    {
        if (cloudSaveService == null) return;

        // Cloud Save API events
        cloudSaveService.OnFullDataLoaded += OnCloudDataLoaded;
        cloudSaveService.OnSaveCompleted += OnCloudSaveCompleted;
        cloudSaveService.OnStatusUpdated += OnStatusUpdated;

        // UserApp API events
        cloudSaveService.OnUserAppLatestLoaded += OnUserAppLatestLoaded;
        cloudSaveService.OnUserAppAllLoaded += OnUserAppAllLoaded;
        cloudSaveService.OnUserAppSaveCompleted += OnUserAppSaveCompleted;
        cloudSaveService.OnUserAppDeleteCompleted += OnUserAppDeleteCompleted;
    }

    private void OnDestroy()
    {
        // Unsubscribe from events
        if (cloudSaveService != null)
        {
            cloudSaveService.OnFullDataLoaded -= OnCloudDataLoaded;
            cloudSaveService.OnSaveCompleted -= OnCloudSaveCompleted;
            cloudSaveService.OnStatusUpdated -= OnStatusUpdated;
            cloudSaveService.OnUserAppLatestLoaded -= OnUserAppLatestLoaded;
            cloudSaveService.OnUserAppAllLoaded -= OnUserAppAllLoaded;
            cloudSaveService.OnUserAppSaveCompleted -= OnUserAppSaveCompleted;
            cloudSaveService.OnUserAppDeleteCompleted -= OnUserAppDeleteCompleted;
        }

        if (loginManager != null)
        {
            loginManager.OnLoginStateChanged -= OnLoginStateChanged;
        }
    }

    // Cloud Save API Handlers
    private void OnLoadCloudSave()
    {
        if (!ValidateLogin()) return;
        UpdateStatus("Loading cloud save...");
        cloudSaveService.LoadCloudSave();
    }

    private void OnSaveCloudSave()
    {
        if (!ValidateLogin()) return;

        // Use current loaded data if available, otherwise use sample data
        CloudSaveService.CloudSaveData saveData;
        if (currentCloudData != null && currentCloudData.data?.userdata != null)
        {
            var userData = currentCloudData.data.userdata;
            saveData = new CloudSaveService.CloudSaveData
            {
                level = userData.level + 1, // Increment level for demo
                @class = userData.@class,
                inventory = new List<string>(userData.inventory)
            };
        }
        else
        {
            saveData = new CloudSaveService.CloudSaveData
            {
                level = sampleLevel,
                @class = sampleClass,
                inventory = new List<string>(sampleInventory)
            };
        }

        UpdateStatus($"Saving Level {saveData.level} {saveData.@class}...");
        cloudSaveService.SaveToCloud(saveData);
    }

    // UserApp API Handlers
    private void OnGetLatest()
    {
        if (!ValidateLogin()) return;
        UpdateStatus("Getting latest user data...");
        cloudSaveService.GetUserAppLatest();
    }

    private void OnSaveUserApp()
    {
        if (!ValidateLogin()) return;

        int level = sampleLevel;
        int score = sampleScore;

        if (levelInput != null && int.TryParse(levelInput.text, out int parsedLevel))
            level = parsedLevel;
        if (scoreInput != null && int.TryParse(scoreInput.text, out int parsedScore))
            score = parsedScore;

        UpdateStatus($"Saving user data - Level: {level}, Score: {score}...");
        cloudSaveService.SaveUserAppData(level, score);
    }

    private void OnGetAll()
    {
        if (!ValidateLogin()) return;
        UpdateStatus("Getting all user data...");
        cloudSaveService.GetUserAppAll();
    }

    private void OnDeleteVersion()
    {
        if (!ValidateLogin()) return;

        if (versionInput == null || !long.TryParse(versionInput.text, out long version) || version <= 0)
        {
            UpdateStatus("Please enter a valid version number");
            return;
        }

        UpdateStatus($"Deleting version {version}...");
        cloudSaveService.DeleteUserAppData(version);
    }

    // Event Handlers
    private void OnCloudDataLoaded(CloudSaveService.CloudSaveResponseWrapper response)
    {
        currentCloudData = response;
        if (response != null && response.data?.userdata != null)
        {
            var data = response.data.userdata;
            string inventoryText = data.inventory != null && data.inventory.Count > 0
                ? string.Join(", ", data.inventory)
                : "Empty";

            string displayText = $"=== CLOUD SAVE DATA ===\n" +
                               $"Record ID: {response.id}\n" +
                               $"User ID: {response.user_id}\n" +
                               $"App ID: {response.app_id}\n" +
                               $"\n=== GAME DATA ===\n" +
                               $"Level: {data.level}\n" +
                               $"Class: {data.@class}\n" +
                               $"Inventory: [{inventoryText}]\n" +
                               $"\n=== TIMESTAMPS ===\n" +
                               $"Last Updated: {data.last_updated}\n" +
                               $"Created: {response.created_at}\n" +
                               $"Updated: {response.updated_at}";

            if (cloudSaveDataText != null)
                cloudSaveDataText.text = displayText;

            UpdateStatus("Cloud save loaded successfully");
            Debug.Log("[CloudSaveUI] Cloud data loaded");
        }
        else
        {
            if (cloudSaveDataText != null)
                cloudSaveDataText.text = "No cloud save found";
            UpdateStatus("No cloud save data available");
        }
    }

    private void OnCloudSaveCompleted(bool success)
    {
        if (success)
        {
            UpdateStatus("Cloud save completed successfully");
            // Auto-reload to show updated data
            OnLoadCloudSave();
        }
        else
        {
            UpdateStatus("Cloud save failed");
        }
    }

    private void OnUserAppLatestLoaded(CloudSaveService.UserAppLatestResponse response)
    {
        currentLatestData = response;
        if (response != null)
        {
            string displayText = $"=== LATEST USER DATA ===\n" +
                               $"Level: {response.data.level}\n" +
                               $"Score: {response.data.score}\n" +
                               $"Version: {response.version}\n" +
                               $"Created: {response.created_at}";

            if (userAppDataText != null)
                userAppDataText.text = displayText;

            UpdateStatus("Latest user data loaded successfully");
            Debug.Log("[CloudSaveUI] Latest user app data loaded");
        }
        else
        {
            if (userAppDataText != null)
                userAppDataText.text = "No latest user data found";
            UpdateStatus("No latest user data available");
        }
    }

    private void OnUserAppAllLoaded(List<CloudSaveService.UserAppData> dataList)
    {
        currentAllData = dataList ?? new List<CloudSaveService.UserAppData>();
        
        if (currentAllData.Count > 0)
        {
            string displayText = $"=== ALL USER DATA ({currentAllData.Count} records) ===\n\n";
            for (int i = 0; i < currentAllData.Count; i++)
            {
                var record = currentAllData[i];
                displayText += $"Record {i + 1}:\n" +
                              $"  Level: {record.data.level}, Score: {record.data.score}\n" +
                              $"  Version: {record.version}\n" +
                              $"  Created: {record.created_at}\n\n";
            }

            if (userAppDataText != null)
                userAppDataText.text = displayText;

            UpdateStatus($"All user data loaded ({currentAllData.Count} records)");
            Debug.Log($"[CloudSaveUI] All user app data loaded - {currentAllData.Count} records");
        }
        else
        {
            if (userAppDataText != null)
                userAppDataText.text = "No user data records found";
            UpdateStatus("No user data records available");
        }
    }

    private void OnUserAppSaveCompleted(bool success)
    {
        if (success)
        {
            UpdateStatus("User data saved successfully");
            // Auto-refresh latest data
            OnGetLatest();
        }
        else
        {
            UpdateStatus("User data save failed");
        }
    }

    private void OnUserAppDeleteCompleted(bool success)
    {
        if (success)
        {
            UpdateStatus("User data deleted successfully");
            // Clear version input and refresh all data
            if (versionInput != null)
                versionInput.text = "";
            OnGetAll();
        }
        else
        {
            UpdateStatus("User data delete failed");
        }
    }

    private void OnStatusUpdated(string message)
    {
        UpdateStatus(message);
    }

    private void OnLoginStateChanged(bool isLoggedIn)
    {
        UpdateButtonStates(isLoggedIn);
        if (isLoggedIn)
        {
            UpdateStatus("Logged in - Ready to use Cloud SDK");
        }
        else
        {
            UpdateStatus("Please login to use Cloud SDK");
            ClearDisplayData();
        }
    }

    // Helper Methods
    private bool ValidateLogin()
    {
        if (loginManager == null)
        {
            UpdateStatus("LoginManager not found");
            return false;
        }

        if (!loginManager.IsLoggedIn)
        {
            UpdateStatus("Please login first");
            return false;
        }

        return true;
    }

    private void UpdateButtonStates(bool enabled)
    {
        if (loadButton != null) loadButton.interactable = enabled;
        if (saveButton != null) saveButton.interactable = enabled;
        if (getLatestButton != null) getLatestButton.interactable = enabled;
        if (saveUserAppButton != null) saveUserAppButton.interactable = enabled;
        if (getAllButton != null) getAllButton.interactable = enabled;
        if (deleteButton != null) deleteButton.interactable = enabled;
    }

    private void UpdateStatus(string message)
    {
        if (statusText != null)
            statusText.text = $"Status: {message}";
        Debug.Log($"[CloudSaveUI] {message}");
    }

    private void ClearDisplayData()
    {
        if (cloudSaveDataText != null)
            cloudSaveDataText.text = "No data loaded";
        if (userAppDataText != null)
            userAppDataText.text = "No data loaded";
        currentCloudData = null;
        currentLatestData = null;
        currentAllData.Clear();
    }
}
```
{% endstep %}

{% step %}
### Wire Serialized Fields

1. Select CloudSaveController in Hierarchy.
2. In Inspector, you'll see CloudSaveUIController component with many empty fields.
3. Set App Id field → enter your VIVERSE App ID (same one used for LoginManager).
4. Drag UI elements from Hierarchy into the corresponding fields:
   * **Load Button** → drag LoadButton from Hierarchy
   * **Save Button** → drag SaveButton from Hierarchy
   * **Cloud Save Data Text** → drag CloudSaveDataText from Hierarchy
   * **Get Latest Button** → drag GetLatestButton from Hierarchy
   * **Save User App Button** → drag SaveUserAppButton from Hierarchy
   * **Get All Button** → drag GetAllButton from Hierarchy
   * **Delete Button** → drag DeleteButton from Hierarchy
   * **Level Input** → drag LevelInput from Hierarchy
   * **Score Input** → drag ScoreInput from Hierarchy
   * **Version Input** → drag VersionInput from Hierarchy
   * **User App Data Text** → drag UserAppDataText from Hierarchy
   * **Status Text** → drag StatusText from Hierarchy

**Tip:** You can also use the circle target picker next to each field to select objects from a popup window.
{% endstep %}
{% endstepper %}

## Step 5. Test all Cloud SDK features

{% stepper %}
{% step %}
### Test Cloud Save API (Legacy)

1. Press **Play** in Unity Editor.
2. Click **Login** (from your LoginManager setup) and complete authentication in the browser.
3. Wait for login to complete—StatusText should show "Logged in - Ready to use Cloud SDK".
4. Test Cloud Save operations:
   * Click **Load Cloud Save** → Check CloudSaveDataText. First time will show "No cloud save found".
   * Click **Save to Cloud** → StatusText should show "Cloud save completed successfully", then data auto-loads.
   * Check CloudSaveDataText → Should now show saved data with level, class, inventory, and timestamps.
   * Click **Save to Cloud** again → Level should increment, data updates.

**What to verify:**

* ✅ Buttons work when logged in
* ✅ Load shows data or "No cloud save found"
* ✅ Save completes successfully
* ✅ Data displays correctly in CloudSaveDataText
* ✅ StatusText shows operation status
{% endstep %}

{% step %}
### Test UserApp API (Versioned)

1.  With user logged in, test UserApp API features:

    **Get Latest:**

    * Click **Get Latest** button
    * Check UserAppDataText → Should show latest level, score, version number, and timestamp
    * If no data exists, will show "No latest user data found"

    **Save User Data:**

    * Enter a level in LevelInput (e.g., "10")
    * Enter a score in ScoreInput (e.g., "500")
    * Click **Save User Data** button
    * StatusText should show "User data saved successfully"
    * UserAppDataText should auto-update with the new data
    * **Note:** Each save creates a new version number

    **Get All Records:**

    * Click **Get All Records** button
    * Check UserAppDataText → Should list all saved versions with their data
    * Note the version numbers shown

    **Delete Version:**

    * Copy a version number from the "Get All" results
    * Paste it into VersionInput field
    * Click **Delete Version** button
    * StatusText should show "User data deleted successfully"
    * UserAppDataText should auto-refresh showing the deleted record is gone

**What to verify:**

* ✅ Get Latest retrieves most recent data
* ✅ Save creates new versions (version numbers increment)
* ✅ Get All shows all historical records
* ✅ Delete removes the specified version
* ✅ StatusText shows operation results
{% endstep %}

{% step %}
### Verify Feature Coverage

Ensure all Cloud SDK features are tested:

* ✅ Cloud Save API: Load
* ✅ Cloud Save API: Save
* ✅ UserApp API: Get Latest
* ✅ UserApp API: Save (creates version)
* ✅ UserApp API: Get All (version history)
* ✅ UserApp API: Delete by version
* ✅ Event system (status updates, completion callbacks)
* ✅ Error handling (no login, invalid data, network errors)
* ✅ Data persistence across sessions
{% endstep %}
{% endstepper %}

## Step 6. Build & deploy to VIVERSE

{% stepper %}
{% step %}
### Switch to WebGL

Switch to WebGL (`File → Build Settings → WebGL`).
{% endstep %}

{% step %}
### Build and Zip

Build; zip the output (`index.html`, `Build/`, `TemplateData/` or `StreamingAssets`).
{% endstep %}

{% step %}
### Upload to VIVERSE Studio

Upload the zip in VIVERSE Studio → Manage Content.
{% endstep %}

{% step %}
### Preview and Submit

Preview and submit for approval. Test all Cloud SDK features in the VIVERSE environment.
{% endstep %}
{% endstepper %}

### Cloud SDK data contracts

### Cloud Save API (Legacy)

**CloudSaveData structure:**

```csharp
{
    public int level;
    public string @class;
    public List<string> inventory;
    public string last_updated;
}
```

**CloudSaveResponseWrapper structure:**

```csharp
{
    public string id;              // Record ID
    public string user_id;         // User account ID
    public string app_id;          // Application ID
    public CloudSaveResponseData data;
    public string created_at;      // ISO timestamp
    public string updated_at;       // ISO timestamp
}
```

**Note:** Cloud Save API uses a single record per user/app. Saving overwrites existing data.

### UserApp API (Versioned)

**UserAppGameData structure:**

```csharp
{
    public int level;
    public int score;
}
```

**UserAppLatestResponse structure:**

```csharp
{
    public string user_id;
    public string app_id;
    public UserAppGameData data;
    public long version;           // Incremental version number
    public string created_at;      // ISO timestamp
}
```

**UserAppData structure (for Get All):**

```csharp
{
    public string user_id;
    public string app_id;
    public UserAppGameData data;
    public long version;
    public string created_at;
}
```

**Note:** UserApp API maintains version history. Each save creates a new version. Versions can be deleted individually.

### Events

**CloudSaveService provides these events:**

* `OnStatusUpdated(string message)` - Status messages for UI feedback
* `OnFullDataLoaded(CloudSaveResponseWrapper response)` - Cloud Save data loaded
* `OnSaveCompleted(bool success)` - Cloud Save operation completed
* `OnUserAppLatestLoaded(UserAppLatestResponse response)` - Latest UserApp data loaded
* `OnUserAppAllLoaded(List<UserAppData> dataList)` - All UserApp records loaded
* `OnUserAppSaveCompleted(bool success)` - UserApp save completed
* `OnUserAppDeleteCompleted(bool success)` - UserApp delete completed

### Authentication Requirements

All Cloud SDK operations require:

* Valid LoginManager instance in the scene
* User must be logged in (access token available)
* App ID must be configured in LoginManager

Cloud SDK automatically validates authentication before each operation and will show appropriate error messages if login is required.

### Troubleshooting

**Buttons don't work:**

* Make sure you're logged in (StatusText should say "Logged in")
* Check that all UI references are wired in CloudSaveUIController Inspector
* Verify EventSystem exists in the scene (auto-created with Canvas)

**"Please login first" error:**

* Ensure LoginManager is in the scene and initialized
* Complete the login flow in browser (for Editor) or via WebGL
* Check that access token is saved (LoginManager.IsLoggedIn should be true)

**UI elements not visible:**

* Check Canvas Render Mode is "Screen Space - Overlay"
* Verify UI elements are children of the Canvas
* Try adjusting RectTransform positions in Inspector
* Check Game view resolution matches your screen

**Data not saving/loading:**

* Verify App ID is set correctly in CloudSaveUIController
* Check Unity Console for error messages
* Ensure network connection is available
* Verify LoginManager has valid access token

**Text fields too small:**

* Select the text field in Hierarchy
* In Inspector, expand RectTransform
* Increase Width and Height values
* Set Text Wrapping Mode to Normal for multi-line text display
