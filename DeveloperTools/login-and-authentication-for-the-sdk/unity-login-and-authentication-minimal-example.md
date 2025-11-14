---
description: >-
  Add VIVERSE login to a brand-new Unity project. You’ll import the VIVERSE
  package, build your own login UI, optionally enable the WebGL bridge, and test
  locally or on VIVERSE.
hidden: true
---

# Unity Login & Authentication Minimal Example

***

### Prerequisites

* Unity 2021 LTS or newer (install the WebGL module if you plan to target WebGL)
* App ID from VIVERSE Studio (https://worlds.viverse.com/)
* VIVERSE login/auth unitypackage (e.g., SDK\_v0.92 1.unitypackage)



{% stepper %}
{% step %}
### Import the VIVERSE package

1. Open Unity and your project.
2. Assets → Import Package → Custom Package…, select the VIVERSE unitypackage, click **Import**.
3. Scripts such as `LoginManager.cs`, `CloudSaveService.cs`, `HttpServer.cs`, and `ViverseSDK 1.jslib` are now available—no code edits needed.
{% endstep %}

{% step %}
### Build the two-column login UI

**Canvas & panel**

1. GameObject → UI → Canvas (name it LoginCanvas).
2. Leave the auto-created EventSystem in the scene.
3. Configure LoginCanvas:
   * Render Mode: Screen Space - Overlay
   * Canvas Scaler → UI Scale Mode: Scale With Screen Size, Reference Resolution: 1920×1080
4.  Create a centered panel:

    * Right-click LoginCanvas → UI → Panel (rename to LoginPanel).
    * In Rect Transform, choose the center anchor preset.
    * Set Width ≈ 1000, Height ≈ 280, Pos X = 0, Pos Y = 0.



**Left column for buttons**

1. Right-click LoginPanel → Create Empty (rename to ButtonColumn).
2. Add a Vertical Layout Group:
   * Padding: all zero (leave the defaults)
   * Spacing: 20
   * Child Alignment: Upper Center
   * Disable Child Force Expand Width/Height.
3. With ButtonColumn selected, set the RectTransform anchor preset to “middle left” (Alt+Left in the anchor matrix).
4. Right-click ButtonColumn → UI → Button (TextMeshPro) (rename to LoginButton). In the RectTransform, set Width = 180, Height = 40; set the child text to “Login”, font size \~24, color a dark gray (#333333).
5. Duplicate LoginButton, rename to ClearDataButton, change the label text to “Clear Data”.
6. Add a Content Size Fitter on ButtonColumn (Horizontal/Vertical Fit = Preferred Size) if you want the column to shrink-wrap the buttons.



**Right column for status labels**

1. Right-click LoginPanel → Create Empty (rename to InfoGroup).
2. Add a Vertical Layout Group:
   * Padding: keep default zeros (0 on all sides)
   * Spacing: 18
   * Child Alignment: Upper Left
3. Set the RectTransform anchor preset to “middle right” (Alt+Right).
4. In the RectTransform, set Width ≈ 500 and Height ≈ 103.
5. Right-click InfoGroup → UI → Text - TextMeshPro (rename to StatusText).
   * Text: “Status: Ready”, font size \~20, color #000000
   * On the TextMeshPro component, set Alignment to Upper Left.
   * In the RectTransform, set Width ≈ 500 and Height ≈ 30.
6. Duplicate StatusText twice:
   * First duplicate → rename the GameObject to AccountText and set its TextMeshPro text to "Account: ".
   * Second duplicate → rename the GameObject to TokenText and set its TextMeshPro text to "Token: ".
   * Keep the same RectTransform Width ≈ 500 and Height ≈ 30 on each duplicate.
7. Add a Content Size Fitter on InfoGroup



**Re-position the columns inside LoginPanel**

* ButtonColumn RectTransform:
  * Anchor Min/Max = (0, 0.5)
  * Pivot = (0, 0.5)
  * Pos X ≈ 110, Pos Y ≈ 0
* InfoGroup RectTransform:
  * Anchor Min/Max = (1, 0.5)
  * Pivot = (1, 0.5)
  * Pos X ≈ -110, Pos Y ≈ 0
{% endstep %}

{% step %}
### Add the controller script

1. Under LoginCanvas, create an empty GameObject named LoginController.
2.  Add this script to LoginController (Unity also adds LoginManager because of the RequireComponent attribute):





    {% code lineNumbers="true" fullWidth="false" %}
    ```csharp
    using UnityEngine;
    using UnityEngine.UI;
    using TMPro;
    using ViverseSDK.Login;   // Import the namespace that exposes LoginManager

    /// <summary>
    /// Handles the UI layer for VIVERSE login. 
    /// It listens to LoginManager events and updates buttons/labels accordingly.
    /// </summary>
    [RequireComponent(typeof(LoginManager))]
    public class LoginUIController : MonoBehaviour
    {
        [Header("App Settings")]
        [SerializeField]
        private string appId;         // Your VIVERSE App ID (assign in Inspector)

        [Header("UI References")]
        [SerializeField] private Button loginButton;
        [SerializeField] private Button clearDataButton;
        [SerializeField] private TMP_Text statusText;
        [SerializeField] private TMP_Text accountText;
        [SerializeField] private TMP_Text tokenText;

        private LoginManager loginManager;

        private void Awake()
        {
            // Grab the LoginManager component and wire up button handlers
            loginManager = GetComponent<LoginManager>();

            loginButton.onClick.AddListener(() => loginManager.StartLogin());
            clearDataButton.onClick.AddListener(() =>
            {
                loginManager.ClearLoginData();
                UpdateStatus("Tokens cleared");
            });

            // Listen to LoginManager events so the UI stays in sync with auth state
            loginManager.OnStatusUpdated += UpdateStatus;
            loginManager.OnTokenUpdated += UpdateToken;
            loginManager.OnUserInfoUpdated += UpdateAccount;
            loginManager.OnLoginStateChanged += ToggleLoginButton;
        }

        private void Start()
        {
            // Show a default status immediately
            UpdateStatus("Ready");

            // Initialize login flow using the App ID specified in Inspector
            loginManager.Initialize(appId);
        }

        private void OnDestroy()
        {
            // Always unsubscribe from events to avoid leaks / stale references
            loginManager.OnStatusUpdated -= UpdateStatus;
            loginManager.OnTokenUpdated -= UpdateToken;
            loginManager.OnUserInfoUpdated -= UpdateAccount;
            loginManager.OnLoginStateChanged -= ToggleLoginButton;
        }

        /// <summary>Updates the status label and logs to console.</summary>
        private void UpdateStatus(string message)
        {
            if (statusText) statusText.text = message;
            Debug.Log("[LoginUI] " + message);
        }

        /// <summary>Shows a truncated access token, to avoid dumping the entire value.</summary>
        private void UpdateToken(string token)
        {
            if (!tokenText) return;

            tokenText.text = string.IsNullOrEmpty(token)
                ? "Token: <empty>"
                : $"Token: {token.Substring(0, Mathf.Min(20, token.Length))}...";
        }

        /// <summary>Displays the account ID returned from the login result.</summary>
        private void UpdateAccount(string accountId)
        {
            if (!accountText) return;

            accountText.text = string.IsNullOrEmpty(accountId)
                ? "Account: <none>"
                : $"Account: {accountId}";
        }

        /// <summary>Disables the login button once a session is active.</summary>
        private void ToggleLoginButton(bool isLoggedIn)
        {
            if (loginButton) loginButton.interactable = !isLoggedIn;
        }
    }
    ```
    {% endcode %}


3. In the Inspector, wire the serialized fields:
   * App Id → your VIVERSE App ID
   * Login Button → LoginButton
   * Clear Data Button → ClearDataButton
   * Status Text → StatusText
   * Account Text → AccountText
   * Token Text → TokenText
{% endstep %}

{% step %}
### Attach HttpServer (Editor/Windows testing)

With **LoginController** selected, add the `HttpServer` component (included in the package). `LoginManager` references it automatically for local redirect flow.
{% endstep %}

{% step %}
### WebGL bridge (only for WebGL builds)

Confirm `Assets/Plugins/WebGL/ViverseSDK 1.jslib` exists (provided by the package). No manual wiring required—`LoginManager` detects WebGL and calls into the bridge automatically.
{% endstep %}

{% step %}
### Test the login flow

* **Editor / Windows**
  1. Press **Play**.
  2. Click **Login** → complete VIVERSE login in the browser.
  3. Status/account/token labels update; data persists in `PlayerPrefs`.
* **WebGL**
  1. Build and host the WebGL player (local server or VIVERSE).
  2. Click **Login** → the `.jslib` handles the SSO flow; the labels update when it succeeds.
{% endstep %}

{% step %}
### Build & deploy to VIVERSE

1. Switch to WebGL (`File → Build Settings → WebGL`).
2. Build; zip the output (`index.html`, `Build/`, `TemplateData/` or `StreamingAssets`).
3. Upload the zip in VIVERSE Studio → Manage Content.
4. Preview and submit for approval.
{% endstep %}
{% endstepper %}

### Login service data contract

LoginManager deserializes login results into:

```csharp
Serializable class Result {
    public string access_token;
    public string account_id;
    public int    expires_in;
    public string state;
}
```

Tokens and account IDs are cached via PlayerPrefs. Clear them with LoginManager.ClearLoginData() when logging out or switching accounts.
