# Dual-Path Authentication Architecture

## WebGL Path

```
┌─────────────────────────────────────────────────────────┐
│  C# (AuthManager.cs)                                     │
│  Initialize(appId) → Login() → OnLoginSuccess event      │
├──────────────────────────────────────────────────────────┤
│  jslib (ViverseAuth.jslib)                               │
│  LoadSDK → InitClient → CheckAuth → Login → Logout       │
├──────────────────────────────────────────────────────────┤
│  viverse-sdk UMD (CDN)                                   │
│  new viverse.Client() → loginWithWorlds/loginWithRedirect │
└──────────────────────────────────────────────────────────┘
```

### Init Logic (ViverseAuth_InitClient)

```javascript
var inIframe = (window !== window.parent);
var clientId = inIframe ? appId : '42ab6113-acc9-419e-93ca-e0734baf9d3d';
var config = { clientId: clientId, domain: domain };
if (!inIframe) {
    config.authorizationParams = { authorities: 'htc.com google.com steam.com' };
}
ViverseAuth_State.client = new globalThis.viverse.Client(config);
```

### Login Logic (ViverseAuth_LoginWithWorlds)

```javascript
var inIframe = (window !== window.parent);
if (inIframe) {
    client.loginWithWorlds();  // postMessage to parent, page refreshes
} else {
    client.loginWithRedirect();  // navigates to OAuth, redirects back
}
```

### Redirect Callback (ViverseAuth_CheckAuth)

On page load, if URL has `?code=&state=`:
```javascript
var result = await client.handleRedirectCallback();
// result = { access_token, account_id, expires_in }
window.history.replaceState({}, document.title, window.location.pathname);
```

## Editor Path

```
┌──────────────────────────────────────────────────────────┐
│  C# (AuthManager.cs)                                      │
│  Initialize(appId) → Login() → OnLoginSuccess event       │
├──────────────────────────────────────────────────────────┤
│  C# (AuthHttpServer.cs)                                   │
│  HttpListener on port 40078                               │
│  GET / → serves login HTML page                           │
│  POST /api/viverse/auth/callback → receives token         │
├──────────────────────────────────────────────────────────┤
│  Browser (login HTML)                                     │
│  Loads viverse-sdk UMD → Client(UUID) → loginWithRedirect │
│  → OAuth redirect → handleRedirectCallback → POST token   │
└──────────────────────────────────────────────────────────┘
```

### Threading

AuthHttpServer runs on a background thread (ThreadPool). Token callback must dispatch to main thread via MainThreadDispatcher (pre-initialized during AuthManager.Initialize).

### MainThreadDispatcher

Created eagerly on main thread during Initialize() to avoid `CreateGameObject` from background thread.

```csharp
MainThreadDispatcher.Initialize(); // Called from AuthManager.Initialize()
MainThreadDispatcher.Enqueue(() => ProcessCallback(body)); // Called from HttpListener thread
```
