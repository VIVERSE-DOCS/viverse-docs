---
description: >-
  Add VIVERSE login to a brand-new Unity project. You’ll import the VIVERSE
  package, build your own login UI, optionally enable the WebGL bridge, and test
  locally or on VIVERSE.
hidden: true
---

# Unity Login & Authentication Minimal Example

#### Prerequisites

* Unity 2021 LTS or newer (install the WebGL module if you plan to target WebGL)
* App ID from VIVERSE Studio
* VIVERSE login/auth unitypackage (e.g., SDK\_v0.92 1.unitypackage)

***

{% stepper %}
{% step %}
### Import the Package

1. Open Unity and your new project.
2. Go to Assets → Import Package → Custom Package…, select the VIVERSE unitypackage, and click Import.
3. Scripts such as LoginManager.cs, CloudSaveService.cs, HttpServer.cs, etc. are imported ready to use—no code edits required.
{% endstep %}

{% step %}
### Create the Login UI

**2.1 Canvas & Panel**

1. GameObject → UI → Canvas (name it LoginCanvas).
2. Unity automatically adds an EventSystem; leave it in the scene.
3. Canvas settings:
   1. Render Mode: Screen Space - Overlay
   2. UI Scale Mode: Scale With Screen Size (e.g., Reference Resolution 1920 × 1080)
4.  Create a centered, fixed-size panel:

    1. Right-click LoginCanvas → UI → Panel (rename to LoginPanel).
    2. In the Inspector’s Rect Transform header (currently showing “stretch”), click the anchor preset square and choose the center preset (anchors and pivot in the middle).
    3. Set Width = 600, Height = 300.
    4. Set Pos X = 0, Pos Y = 0 so LoginPanel sits centered.
    5. Optionally pick a background color for the panel.



**2.2 Buttons**

1. Right-click LoginPanel → UI → Button (TextMeshPro).
2. Rename the button GameObject to LoginButton.
3. Select its child Text (TMP) GameObject and change the displayed text to Login (font size around 24, center alignment).
4. Select the root LoginButton object and set its Rect Transform anchored position to (Pos X = 0, Pos Y = 60)—this moves the entire button up while the child text stays centered at (0, 0).

For the “Clear Data” button:

1. Duplicate LoginButton.
2. Rename the duplicate GameObject to ClearDataButton.
3. Select its child text and change the displayed text to Clear Data.
4. On the root ClearDataButton, set Pos X = 0, Pos Y = 0 (slightly below the login button).



**2.3 Status & Info Text**

1. Right-click LoginPanel → UI → Text - TextMeshPro, rename to StatusText.
   1. Set the text to Status: Ready.
   2. Choose a readable font size (around 20) and color.
   3. Set Pos X = 0, Pos Y = 150 to place it above the buttons.
2. Duplicate this text object twice:
   1. AccountText: set text to Account: \<none>, positioned at (0, 120).
   2. TokenText: set text to Token: \<empty>, positioned at (0, 90).

\> Tip: If you prefer automatic stacking, create an empty child (e.g., InfoGroup), add a Vertical Layout Group (Padding \~10, Spacing \~10, Child Alignment = Middle Center), and make the three text labels children of InfoGroup.



**2.4 Attach Controller Script**

1. Create an empty GameObject under LoginPanel (e.g., LoginController).
2. Add LoginUIController.cs (below).
3. In the Inspector, assign:
   1. Login Button → LoginButton
   2. Clear Data Button → ClearDataButton
   3. Status Text → StatusText
   4. Account Text → AccountText
   5. Token Text → TokenText

```csharp
using UnityEngine;
using UnityEngine.UI;
using TMPro;

[RequireComponent(typeof(LoginManager))]
public class LoginUIController : MonoBehaviour
{
    [Header("UI References")]
    [SerializeField] private Button loginButton;
    [SerializeField] private Button clearDataButton;
    [SerializeField] private TMP_Text statusText;
    [SerializeField] private TMP_Text accountText;
    [SerializeField] private TMP_Text tokenText;

    private LoginManager loginManager;

    private void Awake()
    {
        loginManager = GetComponent<LoginManager>();

        loginButton.onClick.AddListener(() => loginManager.StartLogin());
        clearDataButton.onClick.AddListener(() =>
        {
            loginManager.ClearLoginData();
            UpdateStatus("Tokens cleared");
        });

        loginManager.OnStatusUpdated += UpdateStatus;
        loginManager.OnTokenUpdated += UpdateToken;
        loginManager.OnUserInfoUpdated += UpdateAccount;
        loginManager.OnLoginStateChanged += ToggleLoginButton;
    }

    private void Start()
    {
        UpdateStatus("Ready");
        loginManager.Initialize(loginManager.appId);
    }

    private void OnDestroy()
    {
        loginManager.OnStatusUpdated -= UpdateStatus;
        loginManager.OnTokenUpdated -= UpdateToken;
        loginManager.OnUserInfoUpdated -= UpdateAccount;
        loginManager.OnLoginStateChanged -= ToggleLoginButton;
    }

    private void UpdateStatus(string message)
    {
        if (statusText) statusText.text = message;
        Debug.Log("[LoginUI] " + message);
    }

    private void UpdateToken(string token)
    {
        if (!tokenText) return;
        tokenText.text = string.IsNullOrEmpty(token)
            ? "Token: <empty>"
            : $"Token: {token.Substring(0, Mathf.Min(20, token.Length))}...";
    }

    private void UpdateAccount(string accountId)
    {
        if (!accountText) return;
        accountText.text = string.IsNullOrEmpty(accountId)
            ? "Account: <none>"
            : $"Account: {accountId}";
    }

    private void ToggleLoginButton(bool isLoggedIn)
    {
        if (loginButton) loginButton.interactable = !isLoggedIn;
    }
}
```
{% endstep %}

{% step %}
### Add LoginManager and HttpServer

1. Create another empty GameObject (e.g., LoginManagerGO).
2. Add the provided LoginManager component.
3. Set the App Id field to your VIVERSE App ID.
4. Add an HttpServer component (either on the same GameObject or a new one) and reference it in LoginManager. The HttpServer handles localhost callbacks for Editor/Windows testing.
5. Ensure the GameObject hosting LoginUIController also has (or references) LoginManager, satisfying \[RequireComponent(typeof(LoginManager))].
{% endstep %}

{% step %}
### (WebGL Only): Confirm the WebGL Bridge

* The package includes Assets/Plugins/WebGL/ViverseSDK 1.jslib.
* No additional steps are required: LoginManager automatically calls into this script when building for WebGL.
* Desktop builds rely solely on HttpServer, so .jslib isn’t used there.
{% endstep %}

{% step %}
### Test the Login Flow

1. Editor or Windows build
   1. Press Play and click “Login.”
   2. The browser opens via HttpServer; complete the VIVERSE login.
   3. Unity UI updates with token and account info (stored in PlayerPrefs).
2. WebGL build
   1. Build and host the WebGL output.
   2. Clicking “Login” invokes the .jslib SSO flow.
   3. HandleLoginSuccess (on LoginManager) updates the UI and stores credentials.
{% endstep %}

{% step %}
### Build & Deploy to VIVERSE

1. Switch to WebGL (File → Build Settings → WebGL).
2. Build the project; zip the output (index.html, Build/, TemplateData/).
3. Upload the zip to VIVERSE Studio → Manage Content.
4. Preview and submit for approval.
{% endstep %}
{% endstepper %}



***

#### Login Service Data Contract

LoginManager deserializes login results into:

```csharp
[Serializable]
public class Result {
    public string access_token;
    public string account_id;
    public int    expires_in;
    public string state;
}
```

Tokens and account info are stored via PlayerPrefs ("access\_token", "account\_id", "expires\_in"). Call LoginManager.ClearLoginData() when the user logs out.
