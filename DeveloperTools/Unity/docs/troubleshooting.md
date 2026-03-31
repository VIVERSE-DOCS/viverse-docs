# Troubleshooting

## Play / multiplayer

| Symptom | Things to check |
|--------|-------------------|
| Stuck on “Connecting…” | App ID, room id, user session id; WebGL **browser console** for JS errors; `MULTIPLAYER_PROXY_WS_URL` / `.env` |
| Works in Editor, not WebGL | `play-sdk.jslib` present; correct **WebGL template**; CDN / CORS / network blocking |
| Handoff fails after lobby | Same **game_session**, **app id**, and **actor session_id** passed to `MultiplayerClient.Initialize` as in demo `PlayerPrefs` keys |
| Module init timeout | `MultiplayerInitOptions` JSON, min/max players vs room size, server timing |

---

## Login

| Symptom | Things to check |
|--------|-------------------|
| “APP_ID is empty” | Set App ID on **`LoginSample`** from VIVERSE Studio |
| WebGL **DllImport** errors | Viverse SDK **plugins / template** imported; rebuild WebGL |
| Redirect never completes (Editor) | **`HttpServer`** running; firewall blocking localhost port |
| Token always empty | Clear `PlayerPrefs`, retry login; check browser console |

---

## Avatars

| Symptom | Things to check |
|--------|-------------------|
| “No VRM plugin” | Install **UniVRM**, add **`VRM_0_X`** or **`VRM_1_0`** defines |
| 401 / empty list | Valid **`access_token`** — [Login](login.md) |
| Download / decrypt fails | Network, expired token, encryption IV header — read `AvatarManager` logs |

---

## Cloud saves

| Symptom | Things to check |
|--------|-------------------|
| Buttons disabled | Not logged in — complete **Login** first |
| Save fails | Token, App ID / service config in `CloudSaveService`, server error text in console |

---

## General

- Prefer **one** `EventSystem` / input module in WebGL.  
- Use **LTS Unity** and match **.NET / Newtonsoft** requirements of the samples.  
- Compare your flow to **`MatchmakingDemoManager`** / **`MultiplayerDemoManager`** line-by-line when adapting handoff logic.
