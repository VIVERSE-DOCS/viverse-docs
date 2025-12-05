---
description: >-
  Build a minimal Unity demo showcasing VIVERSE Leaderboard SDK features: query
  rankings, submit scores, and validate data types.
---

# Unity Leaderboard Minimal Example

***

### Prerequisites

* Unity 2021 LTS or newer (install the WebGL module if you plan to target WebGL)
* App ID from VIVERSE Studio (https://worlds.viverse.com/)
* VIVERSE login/auth unitypackage (e.g., SDK\_v0.92 1.unitypackage)
* User must be logged in via LoginManager before using Leaderboard SDK features
* Leaderboard Meta (API Name) created in VIVERSE Studio (https://studio.viverse.com/upload)

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

Scripts such as `LoginManager.cs`, `LeaderboardService.cs`, `LeaderboardSample.cs`, `HttpServer.cs`, and `ViverseSDK .jslib` are now available—no code edits needed.
{% endstep %}
{% endstepper %}

## Step 2. Setup Login (Required for Leaderboard SDK)

{% stepper %}
{% step %}
### Setup Login First

The Leaderboard SDK requires authentication. Follow the Login & Authentication guide to set up:

* LoginManager component
* Login UI with Login button
* App ID configuration
* HttpServer component (for Editor/Windows testing)

Ensure you can successfully log in before proceeding to Leaderboard SDK setup. In the screenshot below, we've created a prefab from the LoginPanel that gets built in the Login & Authentication guide. We've exported the prefab along with LoginUIController.cs
{% endstep %}
{% endstepper %}

## Step 3. Build the Leaderboard SDK UI

{% stepper %}
{% step %}
### Setup Canvas

A. GameObject → UI → Canvas (name it LeaderboardCanvas).

B. Leave the auto-created EventSystem in the scene.

C. Configure LeaderboardCanvas:

* Render Mode: Screen Space - Overlay
* Canvas Scaler → UI Scale Mode: Scale With Screen Size, Reference Resolution: 1920×1080
{% endstep %}

{% step %}
### Create Main Container

A. Right-click LeaderboardCanvas → Create Empty → rename to MainContainer

B. Add Component → Layout → Vertical Layout Group

C. Set Vertical Layout Group properties:

* Child Alignment: Upper Left
* Padding: Left = 20, Right = 20, Top = 20, Bottom = 60
* Spacing: 30
* Child Force Expand: Width = true, Height = false

D. In RectTransform, set Anchor Presets to stretch-stretch (hold Alt+Shift), then set all margins to 0
{% endstep %}

{% step %}
### Create Status Bar Container

A. Right-click MainContainer → Create Empty → rename to StatusBar

* Add Component → Layout → Horizontal Layout Group
* Set Horizontal Layout Group properties:
  * Child Alignment: Middle Left
  * Padding: Left = 20, Right = 20, Top = 10, Bottom = 10
  * Spacing: 0
* In RectTransform, set Height = 40
* Add Component → Layout → Layout Element
* Set Layout Element: Preferred Height = 40, Flexible Height = 0, Flexible Width = 1

B. Right-click StatusBar → UI → Text - TextMeshPro → rename to StatusText

* Text: "Status: Ready - Please login first"
* Font Size: 14
* Alignment: Center
* In RectTransform, set Width = 800, Height = 30
* Add Component → Layout → Layout Element
* Set Layout Element: Preferred Width = 800, Flexible Width = 1
{% endstep %}

{% step %}
### Create Settings Panel

A. Right-click MainContainer → Create Empty → rename to SettingsPanel

* Add Component → Layout → Vertical Layout Group
* Set Vertical Layout Group properties:
  * Child Alignment: Upper Left
  * Padding: All = 15
  * Spacing: 10
  * Child Force Expand: Width = true, Height = false
* In RectTransform, set Width = 450, Height = 300
* Add Component → Layout → Layout Element
* Set Layout Element: Preferred Width = 450, Preferred Height = 300

B. **Create Section Header:**

* Right-click SettingsPanel → UI → Text - TextMeshPro → rename to SettingsHeader
* Text: "Leaderboard Settings"
* Font Size: 24, Font Style: Bold
* Alignment: Left
* In RectTransform, set Height = 35

C. **Create Input Fields Container:**

* Right-click SettingsPanel → Create Empty → rename to InputFieldsContainer
* Add Component → Layout → Vertical Layout Group
* Set Vertical Layout Group properties:
  * Spacing: 8
  * Padding: Top = 5, Bottom = 5
  * Child Force Expand: Width = true, Height = false
* In RectTransform, set Height = 150

D. **Create Input Fields:**

* Right-click InputFieldsContainer → UI → Input Field - TextMeshPro → rename to ApiNameInput
  * Placeholder text: "API Name (Meta Name)"
  * Font Size: 14
  * In the input field's RectTransform, set Height = 35
* Right-click InputFieldsContainer → UI → Input Field - TextMeshPro → rename to ScoreInput
  * Placeholder text: "Score"
  * Font Size: 14
  * In the input field's RectTransform, set Height = 35

E. **Create Data Type Dropdown:**

* Right-click InputFieldsContainer → UI → Dropdown - TextMeshPro → rename to DataTypeDropdown
  * Font Size: 14
  * In RectTransform, set Height = 35
  * Options: "Numeric (Points)", "Seconds (Time)", "Milliseconds (Time)"
{% endstep %}

{% step %}
### Create Buttons Panel

A. Right-click MainContainer → Create Empty → rename to ButtonsPanel

* Add Component → Layout → Vertical Layout Group
* Set Vertical Layout Group properties:
  * Child Alignment: Upper Left
  * Padding: All = 15
  * Spacing: 10
  * Child Force Expand: Width = true, Height = false
* In RectTransform, set Width = 450, Height = 200
* Add Component → Layout → Layout Element
* Set Layout Element: Preferred Width = 450, Preferred Height = 200

B. **Create Section Header:**

* Right-click ButtonsPanel → UI → Text - TextMeshPro → rename to ButtonsHeader
* Text: "Leaderboard Actions"
* Font Size: 24, Font Style: Bold
* Alignment: Left
* In RectTransform, set Height = 35

C. **Create Buttons:**

* Right-click ButtonsPanel → UI → Button (TextMeshPro) → rename to ValidateDataTypeButton
  * Change button text to "Validate Data Type"
  * Font Size: 16
  * In the button's RectTransform, set Height = 40
* Right-click ButtonsPanel → UI → Button (TextMeshPro) → rename to UploadScoreButton
  * Change button text to "Upload Score"
  * Font Size: 16
  * In the button's RectTransform, set Height = 40
* Right-click ButtonsPanel → UI → Button (TextMeshPro) → rename to GetLeaderboardButton
  * Change button text to "Get Leaderboard"
  * Font Size: 16
  * In the button's RectTransform, set Height = 40
{% endstep %}

{% step %}
### Create Leaderboard Display Panel

A. Right-click MainContainer → Create Empty → rename to LeaderboardPanel

* Add Component → Layout → Vertical Layout Group
* Set Vertical Layout Group properties:
  * Child Alignment: Upper Left
  * Padding: All = 15
  * Spacing: 10
  * Child Force Expand: Width = true, Height = false
* In RectTransform, set Width = 600, Height = 500
* Add Component → Layout → Layout Element
* Set Layout Element: Preferred Width = 600, Preferred Height = 500

B. **Create Section Header:**

* Right-click LeaderboardPanel → UI → Text - TextMeshPro → rename to LeaderboardHeader
* Text: "Leaderboard Rankings"
* Font Size: 24, Font Style: Bold
* Alignment: Left
* In RectTransform, set Height = 35

C. **Create Scroll View:**

* Right-click LeaderboardPanel → UI → Scroll View → rename to LeaderboardScrollView
* In RectTransform, set Width = 570, Height = 450
* Remove the default Horizontal Scrollbar (delete it from Hierarchy)
* Select the ScrollRect component and uncheck "Horizontal"

D. **Setup Scroll View Content:**

* Select LeaderboardScrollView → Content (child object)
* Add Component → Layout → Vertical Layout Group
* Set Vertical Layout Group properties:
  * Child Alignment: Upper Left
  * Padding: All = 10
  * Spacing: 5
  * Child Force Expand: Width = true, Height = false
* Add Component → Layout → Content Size Fitter
* Set Content Size Fitter: Vertical Fit = Preferred Size
* In RectTransform, set Width = 550

E. **Create Horizontal Container for Settings and Buttons:**

* Right-click MainContainer → Create Empty → rename to TopRow
* Add Component → Layout → Horizontal Layout Group
* Set Horizontal Layout Group properties:
  * Child Alignment: Upper Left
  * Padding: All = 0
  * Spacing: 30
  * Child Force Expand: Width = false, Height = false
* Move SettingsPanel and ButtonsPanel to be children of TopRow (drag in Hierarchy)
{% endstep %}

{% step %}
### Verify UI Setup

A. **Check Hierarchy Structure:** Your Hierarchy should look like:

```
LeaderboardCanvas
├── EventSystem
└── MainContainer
    ├── StatusBar
    │   └── StatusText
    ├── TopRow
    │   ├── SettingsPanel
    │   │   ├── SettingsHeader
    │   │   └── InputFieldsContainer
    │   │       ├── ApiNameInput
    │   │       ├── ScoreInput
    │   │       └── DataTypeDropdown
    │   └── ButtonsPanel
    │       ├── ButtonsHeader
    │       ├── ValidateDataTypeButton
    │       ├── UploadScoreButton
    │       └── GetLeaderboardButton
    └── LeaderboardPanel
        ├── LeaderboardHeader
        └── LeaderboardScrollView
            └── Viewport
                └── Content
```

B. **Test Layout:**

* Press **Play** to see your UI in the Game view
* Verify all elements are visible and properly organized
* Check that panels are properly arranged with spacing
* Verify status bar is at the top
* Test scroll view functionality

C. **Fine-tune if needed:**

* Adjust spacing in Layout Groups if elements are too close/far
* Modify panel widths if content doesn't fit
* Adjust font sizes for better readability
* Use RectTransform anchors for responsive positioning

D. **Stop Play mode** before continuing to next step.
{% endstep %}
{% endstepper %}

## Step 4. Add the Leaderboard SDK controller script

{% stepper %}
{% step %}
### Create LeaderboardController GameObject

Under LeaderboardCanvas, create an empty GameObject named LeaderboardController.
{% endstep %}

{% step %}
### Add LeaderboardUIController Script

Add this script to LeaderboardController:

```csharp
using UnityEngine;
using UnityEngine.UI;
using TMPro;
using ViverseSDK.Login;
using System.Collections.Generic;
using System.Threading.Tasks;

/// <summary>
/// Handles the UI layer for VIVERSE Leaderboard SDK.
/// Demonstrates all Leaderboard SDK features: Query rankings, submit scores, and data type validation.
/// </summary>
[RequireComponent(typeof(LeaderboardService))]
public class LeaderboardUIController : MonoBehaviour
{
    [Header("App Settings")]
    [SerializeField]
    private string appId;         // Your VIVERSE App ID (assign in Inspector)

    [Header("UI References - Settings")]
    [SerializeField] private TMP_InputField apiNameInput;
    [SerializeField] private TMP_InputField scoreInput;
    [SerializeField] private TMP_Dropdown dataTypeDropdown;

    [Header("UI References - Buttons")]
    [SerializeField] private Button validateDataTypeButton;
    [SerializeField] private Button uploadScoreButton;
    [SerializeField] private Button getLeaderboardButton;

    [Header("UI References - Display")]
    [SerializeField] private Transform leaderboardContainer;  // Content of ScrollView
    [SerializeField] private GameObject leaderboardEntryPrefab;  // Optional prefab for entries
    [SerializeField] private TMP_Text statusText;

    [Header("Sample Data")]
    [SerializeField] private int defaultScore = 100;

    private LeaderboardService leaderboardService;
    private LoginManager loginManager;
    private LeaderboardDataType currentDataType = LeaderboardDataType.Numeric;

    private void Awake()
    {
        // Get required components
        leaderboardService = GetComponent<LeaderboardService>();
        loginManager = FindObjectOfType<LoginManager>();

        if (loginManager == null)
        {
            Debug.LogError("[LeaderboardUI] LoginManager not found! Leaderboard SDK requires login.");
        }

        // Wire up button handlers
        SetupButtons();
        SetupDropdown();
    }

    private void Start()
    {
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
        if (validateDataTypeButton != null)
            validateDataTypeButton.onClick.AddListener(OnValidateDataType);
        if (uploadScoreButton != null)
            uploadScoreButton.onClick.AddListener(OnUploadScore);
        if (getLeaderboardButton != null)
            getLeaderboardButton.onClick.AddListener(OnGetLeaderboard);
    }

    private void SetupDropdown()
    {
        if (dataTypeDropdown != null)
        {
            dataTypeDropdown.onValueChanged.AddListener(OnDataTypeChanged);
            // Ensure dropdown options match enum order
            if (dataTypeDropdown.options.Count == 0)
            {
                dataTypeDropdown.options.Add(new TMP_Dropdown.OptionData("Numeric (Points)"));
                dataTypeDropdown.options.Add(new TMP_Dropdown.OptionData("Seconds (Time)"));
                dataTypeDropdown.options.Add(new TMP_Dropdown.OptionData("Milliseconds (Time)"));
            }
        }
    }

    private void OnDestroy()
    {
        if (loginManager != null)
        {
            loginManager.OnLoginStateChanged -= OnLoginStateChanged;
        }
    }

    // Button Handlers
    private async void OnValidateDataType()
    {
        if (!ValidateLogin()) return;

        string apiName = GetApiName();
        if (string.IsNullOrEmpty(apiName))
        {
            UpdateStatus("Error: Please enter API Name");
            return;
        }

        UpdateStatus("Validating data type...");
        
        string accessToken = GetAccessToken();
        var validationResult = await leaderboardService.ValidateDataTypeAsync(
            appId, 
            accessToken, 
            apiName, 
            currentDataType
        );

        if (validationResult.IsValid)
        {
            UpdateStatus($"Data type validation passed: {GetDataTypeDisplayName(currentDataType)}");
        }
        else
        {
            UpdateStatus($"Data Type Error: {validationResult.ErrorMessage}");
        }
    }

    private async void OnUploadScore()
    {
        if (!ValidateLogin()) return;

        string apiName = GetApiName();
        if (string.IsNullOrEmpty(apiName))
        {
            UpdateStatus("Error: Please enter API Name");
            return;
        }

        float scoreValue = GetScoreValue();
        if (scoreValue < 0)
        {
            UpdateStatus("Error: Please enter a valid score");
            return;
        }

        UpdateStatus($"Uploading score: {FormatScoreDisplay(scoreValue, currentDataType)}...");
        
        string accessToken = GetAccessToken();
        bool success = await leaderboardService.SubmitScoreWithDataTypeAsync(
            appId,
            accessToken,
            apiName,
            scoreValue,
            currentDataType
        );

        if (success)
        {
            UpdateStatus($"Score uploaded successfully! {FormatScoreDisplay(scoreValue, currentDataType)}");
        }
        else
        {
            UpdateStatus("Failed to upload score (check data type or network)");
        }
    }

    private async void OnGetLeaderboard()
    {
        if (!ValidateLogin()) return;

        string apiName = GetApiName();
        if (string.IsNullOrEmpty(apiName))
        {
            UpdateStatus("Error: Please enter API Name");
            return;
        }

        UpdateStatus("Fetching leaderboard...");
        
        string accessToken = GetAccessToken();
        var response = await leaderboardService.GetLeaderboardRecordsAsync(
            appId,
            accessToken,
            apiName,
            0, 9  // Get top 10 records
        );

        if (response != null && response.ranking != null && response.ranking.Length > 0)
        {
            DisplayLeaderboard(response);
            UpdateStatus($"Leaderboard loaded - {response.ranking.Length} players");
        }
        else
        {
            UpdateStatus("No leaderboard data found or failed to retrieve");
            ClearLeaderboardDisplay();
        }
    }

    // Event Handlers
    private void OnLoginStateChanged(bool isLoggedIn)
    {
        UpdateButtonStates(isLoggedIn);
        if (isLoggedIn)
        {
            UpdateStatus("Logged in - Ready to use Leaderboard SDK");
        }
        else
        {
            UpdateStatus("Please login to use Leaderboard SDK");
            ClearLeaderboardDisplay();
        }
    }

    private void OnDataTypeChanged(int index)
    {
        currentDataType = (LeaderboardDataType)(index + 1);  // Enum starts at 1
        Debug.Log($"[LeaderboardUI] Data type changed to: {currentDataType}");
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

    private string GetAccessToken()
    {
        if (loginManager != null && loginManager.IsLoggedIn)
        {
            return loginManager.AccessToken;
        }
        return "";
    }

    private string GetApiName()
    {
        if (apiNameInput != null && !string.IsNullOrEmpty(apiNameInput.text))
        {
            return apiNameInput.text.Trim();
        }
        return "";
    }

    private float GetScoreValue()
    {
        if (scoreInput != null && !string.IsNullOrEmpty(scoreInput.text))
        {
            if (float.TryParse(scoreInput.text, out float value))
            {
                return value;
            }
        }
        return defaultScore;
    }

    private void UpdateButtonStates(bool enabled)
    {
        if (validateDataTypeButton != null) validateDataTypeButton.interactable = enabled;
        if (uploadScoreButton != null) uploadScoreButton.interactable = enabled;
        if (getLeaderboardButton != null) getLeaderboardButton.interactable = enabled;
    }

    private void UpdateStatus(string message)
    {
        if (statusText != null)
            statusText.text = $"Status: {message}";
        Debug.Log($"[LeaderboardUI] {message}");
    }

    private void DisplayLeaderboard(LeaderboardRankingResponse response)
    {
        if (leaderboardContainer == null)
        {
            Debug.LogWarning("[LeaderboardUI] Leaderboard container not assigned");
            return;
        }

        ClearLeaderboardDisplay();

        // Sort by rank
        var sortedRanking = new List<LeaderboardRecord>(response.ranking);
        sortedRanking.Sort((x, y) => x.rank.CompareTo(y.rank));

        foreach (var record in sortedRanking)
        {
            CreateLeaderboardEntry(record);
        }
    }

    private void CreateLeaderboardEntry(LeaderboardRecord record)
    {
        GameObject entryObj;

        if (leaderboardEntryPrefab != null)
        {
            entryObj = Instantiate(leaderboardEntryPrefab, leaderboardContainer);
        }
        else
        {
            // Create simple text entry if no prefab
            entryObj = new GameObject($"Entry_Rank{record.rank}");
            entryObj.transform.SetParent(leaderboardContainer);
            var rectTransform = entryObj.AddComponent<RectTransform>();
            rectTransform.sizeDelta = new Vector2(550, 40);
            
            var tmpText = entryObj.AddComponent<TextMeshProUGUI>();
            tmpText.fontSize = 18;
            tmpText.color = Color.white;
            tmpText.alignment = TextAlignmentOptions.Left;
        }

        // Update text components
        TMP_Text[] tmpTextComponents = entryObj.GetComponentsInChildren<TMP_Text>();
        string formattedScore = FormatScoreDisplay(record.value, currentDataType);
        string displayText = $"#{record.rank + 1} - {record.name} - {formattedScore}";

        if (tmpTextComponents.Length >= 3)
        {
            tmpTextComponents[0].text = $"#{record.rank + 1}";
            tmpTextComponents[1].text = record.name;
            tmpTextComponents[2].text = formattedScore;
        }
        else if (tmpTextComponents.Length == 1)
        {
            tmpTextComponents[0].text = displayText;
        }
    }

    private string FormatScoreDisplay(float value, LeaderboardDataType dataType)
    {
        switch (dataType)
        {
            case LeaderboardDataType.Numeric:
                return $"{value:F0} pts";
            
            case LeaderboardDataType.Seconds:
                int minutes = Mathf.FloorToInt(value / 60f);
                float seconds = value % 60f;
                return $"{minutes:00}:{seconds:00.00}";
            
            case LeaderboardDataType.Milliseconds:
                int totalMs = Mathf.FloorToInt(value);
                int mins = totalMs / 60000;
                int secs = (totalMs % 60000) / 1000;
                int ms = totalMs % 1000;
                return $"{mins:00}:{secs:00}.{ms:000}";
            
            default:
                return $"{value:F0} pts";
        }
    }

    private string GetDataTypeDisplayName(LeaderboardDataType dataType)
    {
        switch (dataType)
        {
            case LeaderboardDataType.Numeric:
                return "Numeric (Points/Score)";
            case LeaderboardDataType.Seconds:
                return "Seconds (Time Format)";
            case LeaderboardDataType.Milliseconds:
                return "Milliseconds (Time Format)";
            default:
                return dataType.ToString();
        }
    }

    private void ClearLeaderboardDisplay()
    {
        if (leaderboardContainer == null) return;

        for (int i = leaderboardContainer.childCount - 1; i >= 0; i--)
        {
            DestroyImmediate(leaderboardContainer.childCount > 0 ? leaderboardContainer.GetChild(i).gameObject : null);
        }
    }
}
```
{% endstep %}

{% step %}
### Wire Serialized Fields

1. Select LeaderboardController in Hierarchy.
2. In Inspector, you'll see LeaderboardUIController component with many empty fields.
3. Set App Id field → enter your VIVERSE App ID (same one used for LoginManager).
4. Drag UI elements from Hierarchy into the corresponding fields:
   * **Api Name Input** → drag ApiNameInput from Hierarchy
   * **Score Input** → drag ScoreInput from Hierarchy
   * **Data Type Dropdown** → drag DataTypeDropdown from Hierarchy
   * **Validate Data Type Button** → drag ValidateDataTypeButton from Hierarchy
   * **Upload Score Button** → drag UploadScoreButton from Hierarchy
   * **Get Leaderboard Button** → drag GetLeaderboardButton from Hierarchy
   * **Leaderboard Container** → drag Content (from LeaderboardScrollView) from Hierarchy
   * **Status Text** → drag StatusText from Hierarchy

**Tip:** You can also use the circle target picker next to each field to select objects from a popup window.
{% endstep %}
{% endstepper %}

## Step 5. Test all Leaderboard SDK features

{% stepper %}
{% step %}
### Test Data Type Validation

1. Press **Play** in Unity Editor.
2. Click **Login** (from your LoginManager setup) and complete authentication in the browser.
3. Wait for login to complete—StatusText should show "Logged in - Ready to use Leaderboard SDK".
4. Test Data Type Validation:
   * Enter your **API Name** (Meta Name) in ApiNameInput field
   * Select the appropriate **Data Type** from dropdown (Numeric, Seconds, or Milliseconds)
   * Click **Validate Data Type** button
   * StatusText should show "Data type validation passed" or an error message if there's a mismatch

**What to verify:**

* ✅ Validation works when logged in
* ✅ Error message appears if data type doesn't match server configuration
* ✅ Success message appears when data type matches
* ✅ StatusText shows validation results

**Note:** The data type must match the configuration in VIVERSE Studio. If you get a mismatch error, check your leaderboard settings in VIVERSE Studio and update the dropdown selection accordingly.
{% endstep %}

{% step %}
### Test Score Submission

1. With user logged in, test Score Submission:
   * Enter your **API Name** in ApiNameInput field
   * Select the correct **Data Type** from dropdown
   * Enter a **Score** value in ScoreInput field
     * For Numeric: Enter a number (e.g., "1000")
     * For Seconds: Enter time in seconds (e.g., "65.5" for 1 minute 5.5 seconds)
     * For Milliseconds: Enter time in milliseconds (e.g., "65500" for 65.5 seconds)
   * Click **Upload Score** button
   * StatusText should show "Score uploaded successfully!"

**What to verify:**

* ✅ Score submission works when logged in
* ✅ Success message appears on successful upload
* ✅ Error message appears if data type validation fails
* ✅ StatusText shows operation results
* ✅ Score is properly formatted based on data type

**Note:** Score submission uses secure encryption (Ironhide). Each submission creates or updates your ranking on the leaderboard.
{% endstep %}

{% step %}
### Test Leaderboard Query

1. With user logged in, test Leaderboard Query:
   * Enter your **API Name** in ApiNameInput field
   * Click **Get Leaderboard** button
   * StatusText should show "Leaderboard loaded - X players"
   * Leaderboard entries should appear in the scroll view

**What to verify:**

* ✅ Leaderboard query works when logged in
* ✅ Top 10 records are displayed
* ✅ Rankings are sorted correctly (rank 0 = #1, rank 1 = #2, etc.)
* ✅ Scores are formatted correctly based on data type
* ✅ Player names and scores are displayed
* ✅ Scroll view works for multiple entries

**Leaderboard Display Format:**

* Each entry shows: Rank, Player Name, and Formatted Score
* Numeric scores: "1000 pts"
* Seconds: "01:05.50" (mm:ss.ff)
* Milliseconds: "01:05.500" (mm:ss.fff)
{% endstep %}

{% step %}
### Verify Feature Coverage

Ensure all Leaderboard SDK features are tested:

* ✅ Data Type Validation (ValidateDataTypeAsync)
* ✅ Score Submission with Data Type (SubmitScoreWithDataTypeAsync)
* ✅ Leaderboard Query (GetLeaderboardRecordsAsync)
* ✅ Secure score encryption (Ironhide)
* ✅ Multiple data types (Numeric, Seconds, Milliseconds)
* ✅ Error handling (no login, invalid API name, data type mismatch)
* ✅ UI feedback (status messages, button states)
* ✅ Score formatting based on data type
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

Preview and submit for approval. Test all Leaderboard SDK features in the VIVERSE environment.
{% endstep %}
{% endstepper %}

### Leaderboard SDK data contracts

### LeaderboardRecord structure

**LeaderboardRecord:**

```csharp
{
    public string uid;          // User ID
    public string name;         // Player display name
    public float value;         // Score value
    public int rank;            // Rank (0-based, 0 = #1)
    public long updatedTime;    // Last update timestamp
    
    // Properties for backwards compatibility
    public string userId => uid;
    public int score => (int)value;
}
```

### LeaderboardMeta structure

**LeaderboardMeta:**

```csharp
{
    public string app_id;
    public string meta_name;              // API Name
    public LeaderboardDisplayName[] display_name;
    public int sort_type;                 // Sort order
    public int update_type;               // Update policy
    public int data_type;                 // 1=Numeric, 2=Seconds, 3=Milliseconds
}
```

### LeaderboardRankingResponse structure

**LeaderboardRankingResponse:**

```csharp
{
    public LeaderboardRecord[] ranking;   // Array of leaderboard records
    public LeaderboardMeta meta;          // Leaderboard metadata
    public int total_count;               // Total number of players
    
    // Properties for backwards compatibility
    public LeaderboardRecord[] records => ranking;
    public string apiName => meta?.meta_name ?? "";
    public int totalCount => total_count;
}
```

### Data Types

**LeaderboardDataType enum:**

```csharp
public enum LeaderboardDataType
{
    Numeric = 1,        // Points/Score (e.g., 1000, 5000)
    Seconds = 2,       // Time in seconds (e.g., 65.5 = 1 min 5.5 sec)
    Milliseconds = 3    // Time in milliseconds (e.g., 65500 = 65.5 sec)
}
```

**Important:** The data type must match the server configuration in VIVERSE Studio. Use `ValidateDataTypeAsync` to check compatibility before submitting scores.

### Score Submission

**ScoreEntry structure:**

```csharp
{
    public string name;   // Leaderboard Meta Name (API Name)
    public string value;  // Score value (string format)
}
```

**Note:** Score submission uses secure encryption (Ironhide) with RSA key exchange and AES encryption. The process involves:

1. Generating RSA key pair
2. Requesting session token from Ironhide API
3. Decrypting symmetric key with RSA private key
4. Encrypting score data with AES
5. Submitting encrypted data to Leaderboard API

### API Methods

**LeaderboardService provides these methods:**

* `ValidateDataTypeAsync(appId, userAuthKey, apiName, clientDataType)` - Validate data type matches server
* `GetLeaderboardRecordsAsync(appId, userAuthKey, apiName, rangeStart, rangeEnd)` - Get leaderboard rankings
* `SubmitScoreWithDataTypeAsync(appId, userAuthKey, apiName, scoreValue, dataType)` - Submit score with validation
* `SubmitScoreSecureAsync(appId, userAuthKey, scores)` - Secure score submission (internal)

### Authentication Requirements

All Leaderboard SDK operations require:

* Valid LoginManager instance in the scene
* User must be logged in (access token available)
* App ID must be configured in LeaderboardUIController
* API Name (Meta Name) must exist in VIVERSE Studio

Leaderboard SDK automatically validates authentication before each operation and will show appropriate error messages if login is required.

### Score Formatting

Scores are automatically formatted based on data type:

* **Numeric:** "1000 pts" (whole numbers)
* **Seconds:** "01:05.50" (mm:ss.ff format)
* **Milliseconds:** "01:05.500" (mm:ss.fff format)

The formatting is handled automatically by `FormatScoreDisplay()` method in the UI controller.

### Troubleshooting

**Buttons don't work:**

* Make sure you're logged in (StatusText should say "Logged in")
* Check that all UI references are wired in LeaderboardUIController Inspector
* Verify EventSystem exists in the scene (auto-created with Canvas)

**"Please login first" error:**

* Ensure LoginManager is in the scene and initialized
* Complete the login flow in browser (for Editor) or via WebGL
* Check that access token is saved (LoginManager.IsLoggedIn should be true)

**"Data Type Error" message:**

* Check your leaderboard configuration in VIVERSE Studio
* Verify the data type setting matches between Unity Inspector and VIVERSE Studio
* Use the "Validate Data Type" button to check compatibility
* Update the Data Type dropdown to match server configuration

**"No leaderboard data found":**

* Verify API Name is correct (case-sensitive)
* Check that the leaderboard exists in VIVERSE Studio
* Ensure at least one score has been submitted to the leaderboard
* Check Unity Console for detailed error messages

**Score submission fails:**

* Verify API Name is correct
* Check data type matches server configuration
* Ensure network connection is available
* Check Unity Console for encryption or network errors
* Verify LoginManager has valid access token

**UI elements not visible:**

* Check Canvas Render Mode is "Screen Space - Overlay"
* Verify UI elements are children of the Canvas
* Try adjusting RectTransform positions in Inspector
* Check Game view resolution matches your screen

**Leaderboard entries not displaying:**

* Verify leaderboardContainer is assigned (should be Content from ScrollView)
* Check that GetLeaderboard was successful (check StatusText)
* Ensure leaderboard has data (submit a score first)
* Check Unity Console for parsing errors

**Score formatting incorrect:**

* Verify Data Type dropdown matches the leaderboard's data type
* Check that score values are in the correct format:
  * Numeric: whole numbers (e.g., 1000)
  * Seconds: decimal seconds (e.g., 65.5)
  * Milliseconds: whole milliseconds (e.g., 65500)

**API Name not found:**

* Verify the API Name (Meta Name) exists in VIVERSE Studio
* Check for typos or case sensitivity issues
* Ensure the leaderboard is created and published in VIVERSE Studio
* Visit https://studio.viverse.com/upload to create or verify leaderboard metas
