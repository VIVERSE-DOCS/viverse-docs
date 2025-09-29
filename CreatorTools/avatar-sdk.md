---
description: How to access .vrm avatar assets for authenticated users.
hidden: true
---

# Avatar SDK

***

VIVERSE provides an identity and avatar system to help users express themselves in 3D worlds, as well as several default public avatars.

> BEFORE GETTING STARTED: you must [authenticate with VIVERSE](developer-tools-old/login-and-authentication-for-the-sdk.md) before requesting user-specific avatars. Non-authenticated users can still make requests to the public avatar list.

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
