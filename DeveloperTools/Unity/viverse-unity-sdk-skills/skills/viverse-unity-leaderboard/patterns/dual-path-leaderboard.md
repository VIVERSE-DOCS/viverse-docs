# Dual-Path Leaderboard Pattern

## Overview

LeaderboardClient uses conditional compilation to select transport:
- **WebGL**: jslib `fetch()` for rankings + viverse-sdk `GameDashboard` for score submission
- **Editor**: `UnityWebRequest` for rankings + `RSACryptoServiceProvider` + AES for encrypted submission

## C# Pattern (LeaderboardClient.cs)

```csharp
#if UNITY_WEBGL && !UNITY_EDITOR
using System.Runtime.InteropServices;
#endif

namespace ViverseSDK
{
    public class LeaderboardClient
    {
        private readonly string _appId;

#if UNITY_WEBGL && !UNITY_EDITOR
        [DllImport("__Internal")]
        private static extern void ViverseLeaderboard_GetRanking(int bridgeId, string gameObjectName,
            string callbackMethod, string appId, string queryParams, string token);

        [DllImport("__Internal")]
        private static extern void ViverseLeaderboard_GetGuestRanking(int bridgeId, string gameObjectName,
            string callbackMethod, string appId, string queryParams);

        [DllImport("__Internal")]
        private static extern void ViverseLeaderboard_SubmitScore(int bridgeId, string gameObjectName,
            string callbackMethod, string appId, string scoresJson, string token);
#endif

        public async Task<LeaderboardResult> SubmitScore(string metaName, string value, string token, CancellationToken ct = default)
        {
            string scoresJson = $"{{\"scores\":[{{\"name\":\"{metaName}\",\"value\":\"{value}\"}}]}}";

#if UNITY_WEBGL && !UNITY_EDITOR
            // Delegates to viverse-sdk GameDashboard (handles RSA/AES internally)
            return await CallWebGL("SubmitScore", (bridgeId, go, cb) =>
                ViverseLeaderboard_SubmitScore(bridgeId, go, cb, _appId, scoresJson, token));
#else
            // Editor: Full RSA/AES Ironhide flow
            using (var rsa = new RSACryptoServiceProvider(2048))
            {
                rsa.PersistKeyInCsp = false;
                string publicKeyBase64 = ExportPublicKeyX509(rsa);

                // GET Ironhide token with public key
                string ironhideUrl = $"{BaseUrl}{IronhidePrefix}?app_id={_appId}&skip_ua=true";
                string sessionResponse = await GetWithPublicKey(ironhideUrl, token, publicKeyBase64, ct);

                // Parse session token + encrypted key
                var sessionDict = MiniJson.Deserialize(sessionResponse) as Dictionary<string, object>;
                string sessionToken = sessionDict["token"].ToString();
                string encryptedKey = sessionDict["key"].ToString();

                // Decrypt symmetric key with RSA
                byte[] symmetricKeyBytes = rsa.Decrypt(Convert.FromBase64String(encryptedKey), false);
                string symmetricKey = Encoding.UTF8.GetString(symmetricKeyBytes);

                // Encrypt scores with AES-CBC
                string encryptedScores = EncryptWithAes(symmetricKey, scoresJson);

                // POST encrypted scores
                string postUrl = $"{BaseUrl}{RankingPrefix}/{_appId}";
                string body = $"{{\"scores\":\"{encryptedScores}\"}}";
                await PostWithSessionToken(postUrl, body, token, sessionToken, ct);

                return new LeaderboardResult { success = true };
            }
#endif
        }
    }
}
```

## jslib Pattern (ViverseLeaderboard.jslib)

```javascript
var LibraryViverseLeaderboard = {

  $Leaderboard_Helpers: {
    // Relative URLs → route through proxy (local) or same-origin (production)
    baseUrl: '/',
    rankingPrefix: 'api/vrleaderboard/v1/apps',

    makeHeaders: function(token, includeContent) {
      var h = {};
      if (includeContent) h['Content-Type'] = 'application/json';
      if (token) {
        var isJwt = token.split('.').length === 3;
        h[isJwt ? 'AccessToken' : 'AuthKey'] = token;
      }
      return h;
    }
  },

  // Rankings: simple fetch with token header
  ViverseLeaderboard_GetRanking: function(bridgeId, goPtr, cbPtr, appIdPtr, queryPtr, tokenPtr) {
    var url = Leaderboard_Helpers.baseUrl + Leaderboard_Helpers.rankingPrefix + '/' + appId + '/metas/ranking?' + queryParams;
    fetch(url, { headers: Leaderboard_Helpers.makeHeaders(token, true) })
      .then(function(r) { return r.json(); })
      .then(function(data) { /* SendMessage success */ })
      .catch(function(e) { /* SendMessage error */ });
  },

  // SubmitScore: use viverse-sdk GameDashboard (has jsencrypt bundled)
  ViverseLeaderboard_SubmitScore: function(bridgeId, goPtr, cbPtr, appIdPtr, scoresPtr, tokenPtr) {
    var gd = new window.viverse.GameDashboard({ baseURL: window.location.origin + '/' });
    var scores = JSON.parse(scoresJson);
    gd.uploadLeaderboardScore(appId, scores.scores, token)
      .then(function(res) { /* SendMessage success */ })
      .catch(function(e) { /* SendMessage error */ });
  }
};
```

## Key Differences from Cloud Save

| Aspect | Cloud Save | Leaderboard |
|--------|-----------|-------------|
| Encryption | None (plain JSON) | RSA + AES (Ironhide) |
| WebGL submit | Direct fetch | viverse-sdk GameDashboard |
| CORS | BCGW allows CORS | viveport.com blocks CORS |
| Proxy needed | No | Yes (local WebGL only) |
| Auth for read | Always required | Optional (guest ranking) |
| Base URL | broadcasting-gateway-gaming | www.viveport.com |

## serve_webgl.sh Proxy Routes

```bash
# Required for local WebGL leaderboard testing
/api/vrleaderboard/* → https://www.viveport.com
/api/ironhide/*      → https://www.viveport.com
```

Without the proxy, all leaderboard API calls from WebGL localhost will fail with CORS errors.
