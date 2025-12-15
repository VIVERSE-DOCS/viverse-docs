---
description: >-
  Add VIVERSE login to a brand-new Unity project. You’ll import the VIVERSE
  package, build your own login UI, optionally enable the WebGL bridge, and test
  locally or on VIVERSE.
---

# Unity Login & Authentication Minimal Example

***

## Overview

Add VIVERSE login to a brand-new Unity project: import the VIVERSE package, build a two-column login UI, optionally enable WebGL support, and test locally or on VIVERSE.

## Prerequisites

* Unity 2021 LTS or newer (install the WebGL module if you plan to target WebGL)
* App ID from VIVERSE Studio (https://worlds.viverse.com/)
* VIVERSE login/auth unitypackage (e.g., SDK\_v0.96.unitypackage)

## Quick Start: Download Pre-built Login Sample

If you prefer to use a pre-configured login sample instead of building it manually, you can download a ready-to-use Unity package that includes the complete login UI setup and scene.

**Download the Login Sample Package:** [Login\_Documentation\_Sample.unitypackage](https://github.com/VIVERSE-DOCS/viverse-docs/blob/467c188919118370850918da29af984ccd25e29c/samples/Unity/Login_Documentation_Sample.unitypackage)

**Important Import Order:** You must import the packages in this specific order to avoid missing dependencies:

1. **First:** Import the VIVERSE SDK Unity package (e.g., `SDK_v0.96.unitypackage`)
   * Assets → Import Package → Custom Package… → Select your SDK package → Import
2. **Second:** Import TextMeshPro (if not already in your project)
   * Window → TextMeshPro → Import TMP Essential Resources
3. **Third:** Import the Login Sample package
   * Assets → Import Package → Custom Package… → Select `Login_Documentation_Sample.unitypackage` → Import

After importing all three packages, open the `Login_Leaderboard_Documentation_Sample` scene from `Assets/Scenes/` to see the complete login example in action.

**Note:** If you prefer to build the login UI manually step-by-step, continue with the instructions below starting from Step 1.

## Step 1. Import the VIVERSE package

{% stepper %}
{% step %}
### Open Unity

Create new project within Unity.
{% endstep %}

{% step %}
### Import VIVERSE Unity Package

Assets → Import Package → Custom Package…, select the VIVERSE unity package, click **Import**.
{% endstep %}

{% step %}
### Review Added Scripts

Scripts such as `LoginManager.cs`, `CloudSaveService.cs`, `HttpServer.cs`, and `ViverseSDK 1.jslib` are now available—no code edits needed.

<figure><img src="../.gitbook/assets/image (13).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}

## Step 2. Build the two-column login UI

{% stepper %}
{% step %}
### Setup Canvas

<figure><img src="../.gitbook/assets/image (31).png" alt="" width="375"><figcaption></figcaption></figure>

A. GameObject → UI → Canvas (name it LoginCanvas).

B. Leave the auto-created EventSystem in the scene.

C. Configure LoginCanvas:

* Render Mode: Screen Space - Overlay
* Canvas Scaler → UI Scale Mode: Scale With Screen Size, Reference Resolution: 1920×1080
{% endstep %}

{% step %}
### Create Centered Panel

A. Right-click LoginCanvas → UI → Panel (rename to LoginPanel).

B. In Rect Transform, choose the center anchor preset.

C. Set Width ≈ 1000, Height ≈ 280, Pos X = 0, Pos Y = 0.
{% endstep %}

{% step %}
### Setup Left Column (Buttons)

A. Right-click LoginPanel → Create Empty (rename to ButtonColumn).

B. Add a Vertical Layout Group to ButtonColumn:

* Padding: all zero (leave the defaults)
* Spacing: 20
* Child Alignment: Upper Center
* Disable Child Force Expand Width/Height.

C. With ButtonColumn selected, set the RectTransform anchor preset to "middle left" (Alt+Left in the anchor matrix).

D. Right-click ButtonColumn → UI → Button (TextMeshPro) (rename to LoginButton). In the RectTransform, set Width = 180, Height = 40; set the child text to "Login", font size \~24, color a dark gray (#333333).

E. Duplicate LoginButton, rename to ClearDataButton, change the label text to "Clear Data".

F. (Optional) Add a Content Size Fitter on ButtonColumn (Horizontal/Vertical Fit = Preferred Size) if you want the column to shrink-wrap the buttons.
{% endstep %}

{% step %}
### Setup Right Column (Info Group)

A. Right-click LoginPanel → Create Empty (rename to InfoGroup).

B. Add a Vertical Layout Group to InfoGroup:

* Padding: keep default zeros (0 on all sides)
* Spacing: 18
* Child Alignment: Upper Left

C. Set the RectTransform anchor preset to "middle right" (Alt+Right). In the RectTransform, set Width ≈ 500 and Height ≈ 103.

D. (Optional) Add a Content Size Fitter on InfoGroup.
{% endstep %}

{% step %}
### Create Status Labels

A. Right-click InfoGroup → UI → Text - TextMeshPro (rename to StatusText):

* Text: "Status: Ready", font size \~20, color #000000
* On the TextMeshPro component, set Alignment to Upper Left.
* In the RectTransform, set Width ≈ 500 and Height ≈ 30.

B. Duplicate StatusText twice:

* First duplicate → rename the GameObject to AccountText and set its TextMeshPro text to "Account: ".
* Second duplicate → rename the GameObject to TokenText and set its TextMeshPro text to "Token: ".
* Keep the same RectTransform Width ≈ 500 and Height ≈ 30 on each duplicate.
{% endstep %}

{% step %}
### Position Columns

A. Set ButtonColumn RectTransform:

* Anchor Min/Max = (0, 0.5)
* Pivot = (0, 0.5)
* Pos X ≈ 110, Pos Y ≈ 0

B. Set InfoGroup RectTransform:

* Anchor Min/Max = (1, 0.5)
* Pivot = (1, 0.5)
* Pos X ≈ -110, Pos Y ≈ 0
{% endstep %}
{% endstepper %}

This two-column layout provides a clean, organized interface for VIVERSE authentication. The left column contains action buttons (Login and Clear Data) that trigger the authentication flow and data management. The right column displays real-time feedback including login status, account information, and token details. This separation of controls and information makes it easy for users to understand the authentication state at a glance while providing clear actions to initiate or reset the login process. The UI will be wired to the LoginUIController script in the next step, which will update these labels dynamically as the authentication flow progresses.

## Step 3. Add the controller script

{% stepper %}
{% step %}
### Add LoginUIController Script

Add this script to **LoginPanel** (Unity also adds LoginManager because of the RequireComponent attribute):

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
{% endstep %}

{% step %}
### Wire Serialized Fields

In the Inspector, wire the serialized fields:

* App Id → your VIVERSE App ID
* Login Button → LoginButton
* Clear Data Button → ClearDataButton
* Status Text → StatusText
* Account Text → AccountText
* Token Text → TokenText

<figure><img src="../.gitbook/assets/image (32).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}

## Step 4. Attach HttpServer (Editor/Windows testing)

{% stepper %}
{% step %}
### Add HttpServer Component

With **LoginPanel** selected, add the `HttpServer` component (included in the package). `LoginManager` references it automatically for local redirect flow.
{% endstep %}
{% endstepper %}

## Step 5. WebGL bridge (only for WebGL builds)

{% stepper %}
{% step %}
### Confirm WebGL Bridge File

Confirm `Assets/Plugins/WebGL/ViverseSDK 1.jslib` exists (provided by the package). No manual wiring required—`LoginManager` detects WebGL and calls into the bridge automatically.

<figure><img src="../.gitbook/assets/image (15).png" alt="" width="198"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}

## Step 6. Test the login flow

{% stepper %}
{% step %}
### Editor / Windows Testing

A. Press **Play**.

B. Click **Login** → complete VIVERSE login in the browser.

C. Verify that status/account/token labels update; data persists in `PlayerPrefs`.
{% endstep %}

{% step %}
### WebGL Testing

A. Build and host the WebGL player (local server or VIVERSE).

B. Click **Login** → the `.jslib` handles the SSO flow; the labels update when it succeeds.
{% endstep %}
{% endstepper %}

## Step 7. Build & deploy to VIVERSE

{% stepper %}
{% step %}
### Publishing to Viverse

Publishing to Viverse documentation can be found [here](https://app.gitbook.com/o/SnIK7SeXTWk0ghDScPhF/s/4pMiThqqrBzfvP8uy5am/standalone-publishing/publishing-to-viverse-with-viverse-studio).
{% endstep %}
{% endstepper %}

***

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
