---
description: Track high scores and other ranked data with the VIVERSE Leaderboard SDK
---

# Leaderboard SDK

***

> **BEFORE GETTING STARTED:** you must [authenticate with VIVERSE](../login-and-authentication-for-the-sdk/) before requesting leaderboard services.

### Leaderboard Setup in VIVERSE Studio

Before integrating the Leaderboard SDK, you must first configure the leaderboard metadata settings for your content in VIVERSE Studio.

1.  Go to the Upload section in the sidebar to open the "Manage Content" page in VIVERSE Studio.\\

    <figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
2. Click "Upload Content" for the world you want to edit, then navigate to the SDK Settings tab, and click Add New Leaderboard.\
   \
   ![](<../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1).png>)\\
3. In the Leaderboard Configuration section, define the necessary leaderboard parameters. **This configuration is required** to enable proper interaction between your content and the leaderboard system.\
   \
   ![](<../.gitbook/assets/image (5) (1).png>)

### Game Dashboard Client Setup

To then make use of your VIVERSE Leaderboard, you must instantiate the `gameDashboardClient` [after authentication](../broken-reference/).

```
// If the user is logged in, get the token; if not, return `undefined`
const accessToken = await globalThis.viverseClient.getToken();

// initialize game dashboard client instance
globalThis.gameDashboardClient = new globalThis.viverse.gameDashboard({
    baseURL: 'https://www.viveport.com/',
    communityBaseURL: 'https://www.viverse.com/',
    token: accessToken
});
```

Then call `uploadLeaderboardScore()` to change leaderboard data:

```
// Can upload multiple leaderboard scores at once
const scores = [
    { name: "highScores", value: "100.0" },
    { name: "numberOfTimesPlayed", value: "60.0" }
];

const uploadLeaderboard = await globalThis.gameDashboardClient.uploadLeaderboardScore(appID, scores)
```

Or call `getLeaderboard()` to check scores:

<pre><code>// A valid leaderboardConfig example:
const leaderboardConfig = {
    name: "highScores",  // string
    range_start: 0,  // number, show number of users beyond user's rank
    range_end: 100,  // number, show number of users below user's rank
    region: "global",  // string, get by local/global
    time_range: "alltime",
    around_user: false
<strong>};
</strong>
const leaderboard = await globalThis.gameDashboardClient.getLeaderboard(appID, leaderboardConfig);
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

## API Reference

### `new viverse.gameDashboard(options)`

Initializes a new Game Dashboard client instance.

| Parameter            | Type     | Description                                                                         | Required |
| -------------------- | -------- | ----------------------------------------------------------------------------------- | -------- |
| `options`            | `object` | An object containing configuration for the client.                                  | Yes      |
| ↳ `baseURL`          | `string` | The base URL for the Viveport API. Should be `https://www.viveport.com/`.           | Yes      |
| ↳ `communityBaseURL` | `string` | The base URL for the VIVERSE community API. Should be `https://www.viverse.com/`.   | Yes      |
| ↳ `token`            | `string` | The access token obtained from the VIVERSE client. Required for user-specific data. | Yes      |

***

### `uploadLeaderboardScore(appID, scores)`

Uploads one or more scores to the leaderboard.

| Parameter | Type           | Description                                       | Required |
| --------- | -------------- | ------------------------------------------------- | -------- |
| `appID`   | `string`       | Your application's unique ID from VIVERSE Studio. | Yes      |
| `scores`  | `Array<Score>` | An array of `Score` objects to upload.            | Yes      |

**Score Object**

| Property | Type     | Description                                                                                                    |
| -------- | -------- | -------------------------------------------------------------------------------------------------------------- |
| `name`   | `string` | The name of the leaderboard to upload the score to. This corresponds to the name configured in VIVERSE Studio. |
| `value`  | `string` | The score value to upload.                                                                                     |

***

### `getLeaderboard(appID, config)`

Retrieves leaderboard data based on the provided configuration.

| Parameter | Type     | Description                                       | Required |
| --------- | -------- | ------------------------------------------------- | -------- |
| `appID`   | `string` | Your application's unique ID from VIVERSE Studio. | Yes      |
| `config`  | `object` | A configuration object for the leaderboard query. | Yes      |

**Leaderboard Config Object**

| Property      | Type      | Description                                                     |
| ------------- | --------- | --------------------------------------------------------------- |
| `name`        | `string`  | The name of the leaderboard to retrieve.                        |
| `range_start` | `number`  | The starting rank for the query.                                |
| `range_end`   | `number`  | The ending rank for the query.                                  |
| `region`      | `string`  | The region for the leaderboard. Can be `"global"` or `"local"`. |
| `time_range`  | `string`  | The time range for the leaderboard. e.g., `"alltime"`.          |
| `around_user` | `boolean` | Whether to fetch ranks around the current user.                 |

***

### `getGuestLeaderboard(appID, config)`

Retrieves leaderboard data based on the provided configuration.

| Parameter | Type     | Description                                       | Required |
| --------- | -------- | ------------------------------------------------- | -------- |
| `appID`   | `string` | Your application's unique ID from VIVERSE Studio. | Yes      |
| `config`  | `object` | A configuration object for the leaderboard query. | Yes      |

**Leaderboard Config Object**

| Property       | Type     | Description                                                                                     |
| -------------- | -------- | ----------------------------------------------------------------------------------------------- |
| `name`         | `string` | The name of the leaderboard to retrieve.                                                        |
| `range_start`  | `number` | The starting rank for the query.                                                                |
| `range_end`    | `number` | The ending rank for the query.                                                                  |
| `region`       | `string` | The region for the leaderboard. Can be `"global"` or `"local"`.                                 |
| `time_range`   | `string` | The time range for the leaderboard. e.g., `"alltime"`.                                          |
| `country_code` | `string` | The language of the leaderboard name. ISO 3166-1 alpha-2 country code, now is only support 'US' |

***

### Leaderboard Response Object

The `getLeaderboard` and `getGuestLeaderboard` methods returns a `Promise` that resolves to an object with the following structure.

| Property      | Type                  | Description                                                |
| ------------- | --------------------- | ---------------------------------------------------------- |
| `ranking`     | `Array<RankingEntry>` | An array of objects representing the leaderboard rankings. |
| `meta`        | `object`              | Metadata about the leaderboard.                            |
| `total_count` | `number`              | The total number of entries in the leaderboard.            |

**RankingEntry Object**

| Property | Type     | Description                         |
| -------- | -------- | ----------------------------------- |
| `uid`    | `string` | The unique ID of the user.          |
| `name`   | `string` | The display name of the user.       |
| `value`  | `number` | The user's score.                   |
| `rank`   | `number` | The user's rank in the leaderboard. |

**Meta Object**

| Property       | Type            | Description                                                                                                                |
| -------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------- |
| `app_id`       | `string`        | The application ID.                                                                                                        |
| `meta_name`    | `string`        | The internal name of the leaderboard.                                                                                      |
| `display_name` | `Array<object>` | An array of display names for different languages.                                                                         |
| ↳ `lang`       | `string`        | The language code (e.g., `"en-US"`).                                                                                       |
| ↳ `name`       | `string`        | The display name in the specified language.                                                                                |
| `sort_type`    | `number`        | The method used for sorting scores (e.g., ascending, descending).                                                          |
| `update_type`  | `number`        | The rule for updating scores (e.g., append to add all submitted scores, or update to only store the user's highest score). |
| `data_type`    | `number`        | The data type of the score value.                                                                                          |
