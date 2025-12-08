---
description: Track high scores and other ranked data with the VIVERSE Leaderboard SDK
hidden: true
---

# Leaderboard SDK



***

> **BEFORE GETTING STARTED:** you must [authenticate with VIVERSE](developer-tools-old/login-and-authentication-for-the-sdk.md) before requesting leaderboard services.

### Leaderboard Setup in VIVERSE Studio

Before integrating the Leaderboard SDK, you must first configure the leaderboard metadata settings for your content&#x20;in VIVERSE Studio.

1.  Go to the Upload section in the sidebar to open the "Manage Content" page in VIVERSE Studio.<br>

    <figure><img src=".gitbook/assets/image (726).png" alt=""><figcaption></figcaption></figure>
2.  Click "Upload Content" for the world you want to edit, then navigate to the SDK Settings tab.<br>

    <figure><img src=".gitbook/assets/image (724).png" alt=""><figcaption></figcaption></figure>
3.  In the Leaderboard Configuration section, define the necessary leaderboard parameters. **This configuration is required** to enable proper interaction between your content and the leaderboard    &#x20;system.<br>

    <figure><img src=".gitbook/assets/image (725).png" alt=""><figcaption></figcaption></figure>

### Game Dashboard Client Setup

To then make use of your VIVERSE Leaderboard, you must instantiate the `gameDashboardClient` [after authentication](developer-tools-old/login-and-authentication-for-the-sdk.md).

```
// If the user is logged in, get the token; if not, return `undefined`
const accessToken = await globalThis.viverseClient.getToken();

// initialize game dashboard client instance
globalThis.gameDashboardClient = new globalThis.viverse.gameDashboard({
    baseURL: 'https://www.viveport.com/',
    communityBaseURL: 'https://www.viverse.com/',
    token: { accessToken }
});
```

&#x20;Then call `uploadLeaderboardScore()` to change leaderboard data:

```
// Can upload multiple leaderboard scores at once
const scores = [
    { name: "high_scores", value: "100.0" },
    { name: "number_of_times_played", value: "60.0" }
];

const uploadLeaderboard = await globalThis.gameDashboardClient.uploadLeaderboardScore(appID, scores)
```

Or call `getLeaderboard()` to check scores:

<pre><code>// A valid leaderboardConfig example:
const leaderboardConfig = {
    name: "high_scores",  // string
    range_start: 0,  // number, show number of users beyond user's rank
    range_end: 100,  // number, show number of users below user's rank
    region: "global",  // string, get by local/global
    time_range: "alltime",
    around_user: false
<strong>};
</strong>
const leaderboard = await globalThis.gameDashboardClient.getLeaderboard(appID, config);
</code></pre>

The response will be formatted like so, which you can integrate into your game's UI and logic:

<pre class="language-javascript"><code class="lang-javascript">{
    ranking: [
        {
<strong>            uid: string,
</strong>            name: string,
            value: number,  // the score or stored data
            rank: number,  // the rank of the user's score
        }
    ],
<strong>    meta: {
</strong>        app_id: string,
<strong>        meta_name: string,
</strong>        display_name: [
            {
<strong>                lang: string,
</strong>                name: string
            }
        ],
        sort_type: number,
        update_type: number,
            data_type: number,
    },
    total_count: number
}
</code></pre>

a
