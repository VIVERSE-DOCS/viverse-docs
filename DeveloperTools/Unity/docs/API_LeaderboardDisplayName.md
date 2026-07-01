# Leaderboard Community Display Name

## Overview

After fetching leaderboard rankings, `LeaderboardService` automatically resolves each player's VIVERSE community display name by calling the community service API. The resolved name overwrites the raw `name` field on each `LeaderboardRecord` before the response is returned to the caller.

---

## API Used

| | |
|---|---|
| **Method** | `POST` |
| **URL** | `https://www.viverse.com/api/community-service/v1/users/get` |
| **Auth** | Not required |

### Request Body

```json
{
  "targetUserIriList": [
    "https://www.viverse.com/users/<uid>",
    "https://www.viverse.com/users/<uid>"
  ]
}
```

### Response

```json
{
  "data": [
    {
      "name": "DisplayName",
      "preferredUsername": "username",
      "userIri": "https://www.viverse.com/users/<uid>"
    }
  ]
}
```

> **Note:** This endpoint only returns entries for users who have interacted within the VIVERSE federation. Players with no community activity may not appear in the response — their `name` field will retain the original value from the leaderboard API.

---

## How It Works

1. `GetLeaderboardRecordsAsync` fetches the ranking from the Viveport leaderboard API.
2. All `uid` values are collected from the ranking records.
3. Each `uid` is converted to a user IRI: `https://www.viverse.com/users/<uid>`
4. The IRI list is posted to the community service API.
5. The response is mapped `userIri → name` using a dictionary.
6. Each `LeaderboardRecord.name` is replaced with the community display name.

```
Leaderboard API response
  └─ ranking[].uid  ──► build IRI list
                             │
                             ▼
              POST /api/community-service/v1/users/get
                             │
                             ▼
              data[].userIri + data[].name
                             │
                             ▼
              ranking[].name = displayNameMap[userIri]
```

---

## Fallback Behavior

| Situation | Result |
|---|---|
| Community API call fails | Original `name` from leaderboard is kept |
| User not found in community response | Original `name` from leaderboard is kept |
| Original `name` is also empty | `"User"` is used (currently only logs a warning) |

---

## Relevant Files

| File | Role |
|---|---|
| `Assets/Script/LeaderboardService.cs` | Contains `GetCommunityDisplayNamesAsync` and the mapping logic inside `GetLeaderboardRecordsAsync` |
| `Assets/Script/LeaderboardSample.cs` | UI sample — calls `GetLeaderboardRecordsAsync` and renders `record.name` |

---

## Key Constants (`LeaderboardService.cs`)

```csharp
private const string COMMUNITY_BASE_HOST      = "https://www.viverse.com/";
private const string COMMUNITY_DISPLAY_NAME_API_URL = COMMUNITY_BASE_HOST + "api/community-service/v1/users/get";
private const string TARGET_USER_IRI_BASE_URL = COMMUNITY_BASE_HOST;
```
