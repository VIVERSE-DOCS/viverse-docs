---
description: How to access .vrm avatar assets for authenticated users.
---

# Avatar SDK

***

VIVERSE provides an identity and avatar system to help users express themselves in 3D worlds, as well as several default public avatars.

> BEFORE GETTING STARTED: you must [authenticate with VIVERSE](login-and-authentication-for-the-sdk.md) before requesting user-specific avatars. Non-authenticated users can still make requests to the public avatar list.

## Getting Started

After authenticating, use `getToken()`  to get an access token to make requests for user data to the VIVERSE Avatar SDK.

<pre><code>// If authenticated, you will receive a token, otherwise 'undefined'
const accessToken = (await globalThis.viverseClient.getToken()) as string | undefiend;

// initialize avatar client instance
globalThis.avatarClient = new globalThis.viverse.avatar({
<strong>    baseURL: 'https://sdk-api.viverse.com/', // VIVERSE API domain
</strong>    token: accessToken, // required to query user-specific data
});
</code></pre>

Once the avatar client is instantiated, you have access to the following methods:

*   `getActiveAvatar()`: get user's default, active avatar, formatted as:\


    <pre><code>{
        id: string | number;
        isPrivate: boolean;
        vrmUrl: string;
        headIconUrl: string;
        snapshot: string;
        createTime: number;
    <strong>    updateTime: number;
    </strong>}
    </code></pre>


* `getAvatarList()`: get user's avatar list. This will return an array of objects formatted as above.
*   `getProfile()`: get the user's display name and active avatar. The return object is formatted as:\


    ```
    {
        name: string;
        activeAvatar: Avatar | null;
    }
    ```


* `getPublicAvatarList()`: get the list of default, publicly-available VIVERSE avatars as an array of objects formatted as above.
* `getPublicAvatarById(id)`: get a specific public avatar according to a known ID.
* `getAvatarFileWithSDK(url)`: provide a URL to an avatar asset to load its array buffer. Returns an array buffer.

## API Reference

### `new viverse.avatar(options)`

Initializes a new VIVERSE Avatar client instance.

| Parameter | Type | Description | Required |
| :--- | :--- | :--- | :--- |
| `options` | `object` | An object containing configuration for the client. | Yes |
| ↳ `baseURL` | `string` | The VIVERSE API domain. Should be `https://sdk-api.viverse.com/`. | Yes |
| ↳ `token` | `string` | The access token obtained from the VIVERSE client. Required for user-specific data. | No |

---

### Avatar Object

Several methods return an `Avatar` object or an array of `Avatar` objects. This object has the following structure:

| Property | Type | Description |
| :--- | :--- | :--- |
| `id` | `string \| number` | The unique ID of the avatar. |
| `isPrivate` | `boolean` | Whether the avatar is private to the user. |
| `vrmUrl` | `string` | The URL to the `.vrm` file for the avatar. |
| `headIconUrl` | `string` | The URL to the headshot icon of the avatar. |
| `snapshot` | `string` | A snapshot image of the avatar. |
| `createTime` | `number` | The timestamp of when the avatar was created. |
| `updateTime` | `number` | The timestamp of the last update to the avatar. |

---

### `getActiveAvatar()`

Retrieves the user's currently active avatar.
**Returns:** `Promise<Avatar>` - A promise that resolves to the user's active `Avatar` object.

---

### `getAvatarList()`

Retrieves the list of all avatars belonging to the authenticated user.
**Returns:** `Promise<Avatar[]>` - A promise that resolves to an array of `Avatar` objects.

---

### `getProfile()`

Retrieves the user's profile information, including their display name and active avatar.
**Returns:** `Promise<object>` - A promise that resolves to an object containing profile information.

**Return Object Properties**
| Property | Type | Description |
| :--- | :--- | :--- |
| `name` | `string` | The user's display name. |
| `activeAvatar` | `Avatar \| null` | The user's active `Avatar` object, or `null` if none is set. |

---

### `getPublicAvatarList()`

Retrieves a list of all publicly available VIVERSE avatars.
**Returns:** `Promise<Avatar[]>` - A promise that resolves to an array of public `Avatar` objects.

---

### `getPublicAvatarById(id)`

Retrieves a specific public avatar by its ID.

| Parameter | Type | Description | Required |
| :--- | :--- | :--- | :--- |
| `id` | `string \| number` | The ID of the public avatar to retrieve. | Yes |

**Returns:** `Promise<Avatar>` - A promise that resolves to the requested public `Avatar` object.

---

### `getAvatarFileWithSDK(url)`

Loads the file for an avatar from a given URL.

| Parameter | Type | Description | Required |
| :--- | :--- | :--- | :--- |
| `url` | `string` | The URL of the avatar asset (`.vrm` file). | Yes |

**Returns:** `Promise<ArrayBuffer>` - A promise that resolves to the avatar file as an `ArrayBuffer`.