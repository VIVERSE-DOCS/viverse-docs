---
description: >-
  Complete guide for the share button functionality on VIVERSE world pages,
  including location, user interaction flow, sharing options, and
  platform-specific behavior.
---

# VIVERSE Share Button Functionality

***

## Overview

The share button on VIVERSE world pages allows users to share their VIVERSE worlds with others through various platforms and methods. This guide covers the button's location, functionality, available sharing options, and how to use it effectively to share VIVERSE content with friends, colleagues, or the broader community.

## Prerequisites

* Active VIVERSE account
* Access to a VIVERSE world page (e.g., https://worlds.viverse.com/\[WorldID])
* Web browser or VIVERSE mobile app
* Understanding of how to navigate VIVERSE worlds

## Share Button Location and Access

{% stepper %}
{% step %}
### Locating the Share Button

The share button is typically located on VIVERSE world pages in one of the following locations:

* **Top-right corner** of the world page interface
* **Below the world description** or title area
* **Within the world information panel** or sidebar
* **Mobile apps**: May appear in the action menu or top navigation bar

**Visual Appearance:**

* Share icon (typically a connected nodes icon or arrow-out icon)
* May be labeled "Share" or display as icon-only
* Button styling matches VIVERSE's design system
{% endstep %}

{% step %}
### Accessing Share Options

A. Navigate to the VIVERSE world page you want to share\
B. Locate and click/tap the share button\
C. A share menu appears displaying four options: Twitter, Facebook, Copy Link, and Embed\
D. Select your preferred sharing method from the available options
{% endstep %}
{% endstepper %}

## Sharing Options and Methods

When you click the Share button, a menu appears with the following four options:

### Twitter

**Functionality:**

* Opens Twitter/X share dialog in a new window or popup
* Pre-fills tweet with world link
* Allows user to add custom message before posting

**Usage:**

1. Click the share button
2. Select the Twitter option
3. Twitter share dialog opens with world link
4. Add optional message and post to Twitter

### Facebook

**Functionality:**

* Opens Facebook share dialog in a new window or popup
* Pre-fills share with world link and metadata
* Includes world thumbnail, title, and description in preview

**Usage:**

1. Click the share button
2. Select the Facebook option
3. Facebook share dialog opens with world preview
4. Add optional message and post to Facebook timeline

### Copy Link

**Functionality:**

* Copies the world's URL directly to your clipboard
* URL format: `https://worlds.viverse.com/[WorldID]` (may include tracking parameters)
* Can be pasted into any application, message, or document

**Usage:**

1. Click the share button
2. Click the "Copy Link" button
3. Link is copied to clipboard
4. Paste the link where you want to share it

**URL Parameters:**

* May include tracking parameters (`_gl`, `_ga`, `_gcl_au`, etc.)
* World ID remains the core identifier
* Parameters help track share sources for analytics

### Embed

**Functionality:**

* Provides HTML embed code for the VIVERSE world
* Allows embedding world preview on external websites
* Includes iframe code with appropriate dimensions

**Usage:**

1. Click the share button
2. Select the Embed option
3. Embed code dialog/modal appears with HTML code
4. Copy the embed code
5. Paste the code into your website's HTML where you want the world to appear

**Embed Code Details:**

* Code is typically an iframe element
* Includes responsive sizing attributes
* May include parameters for customization (e.g., width, height, autoplay)

## User Interaction Flow

{% stepper %}
{% step %}
## Initiating Share

A. User navigates to a VIVERSE world page\
B. User identifies the share button in the interface\
C. User clicks or taps the share button\
D. Share menu/dialog appears
{% endstep %}

{% step %}
### Selecting Share Method

1. User reviews the four available sharing options in the share menu
2. User selects preferred sharing method:
   * **Twitter** - Share to Twitter/X
   * **Facebook** - Share to Facebook
   * **Copy Link** - Copy world URL to clipboard
   * **Embed** - Get embed code for websites
3. System processes the selection
{% endstep %}

{% step %}
### Completing Share Action

**For Twitter:**

* Twitter share dialog opens in new window/popup
* World link is pre-filled
* User adds optional message and posts to Twitter

**For Facebook:**

* Facebook share dialog opens in new window/popup
* World link, thumbnail, and metadata are pre-filled
* User adds optional message and posts to Facebook timeline

**For Copy Link:**

* URL is copied to clipboard
* Confirmation message appears (e.g., "Link copied!")
* User can paste link in desired location

**For Embed:**

* Embed code dialog/modal appears
* HTML embed code is displayed and ready to copy
* User copies code and pastes into their website HTML
{% endstep %}
{% endstepper %}

## Platform-Specific Behavior

### Desktop Browsers

**Features:**

* Share menu displays all four options (Twitter, Facebook, Copy Link, Embed)
* Hover states on share button and menu options
* Keyboard accessibility support
* Clicking outside the menu closes it

**Browser Compatibility:**

* Modern browsers (Chrome, Firefox, Safari, Edge)
* Share options open in appropriate popups/dialogs
* Copy Link uses clipboard API (requires HTTPS or localhost)

### Mobile Browsers

**Features:**

* Touch-optimized share button and menu
* All four sharing options available (Twitter, Facebook, Copy Link, Embed)
* Responsive design for smaller screens
* Menu adapts to mobile viewport

**Mobile App:**

* Same four sharing options as web version
* Optimized touch interactions
* Native app integration where applicable

### Access Control Considerations

**World Visibility:**

* **Public worlds**: Shareable by anyone with access
* **Private/Unlisted worlds**: Shareable, but recipients need appropriate permissions
* **Password-protected worlds**: Shared link includes access requirements

**Privacy:**

* Users should verify world visibility settings before sharing
* Shared links respect world privacy settings
* Recipients may need to sign in or enter passcode

## Best Practices

**Before Sharing:**

* Verify world is set to appropriate visibility (public/unlisted/private)
* Test the link to ensure it works correctly
* Confirm world content is ready for sharing
* Check that passcode information is communicated if needed

**When Sharing:**

* Include context about the world when sharing with others
* Use appropriate sharing method for your audience
* Consider privacy implications of sharing publicly
* Share during active engagement times for better response

**Link Management:**

* Shared links remain valid as long as the world exists
* Updating world settings may affect link accessibility
* Deleted worlds will result in broken links

## Troubleshooting

**Share Button Not Visible:**

* Check if you have appropriate permissions for the world
* Verify you're logged into your VIVERSE account
* Try refreshing the page
* Check if world is in a shareable state

**Link Not Copying:**

* Check browser permissions for clipboard access
* Try using a different browser
* Use alternative sharing method (e.g., manual copy)
* Verify JavaScript is enabled in browser

**Share Preview Not Working:**

* Ensure world has proper metadata (title, description, thumbnail)
* Check Open Graph tags in page source
* Verify world is publicly accessible for preview generation

**Mobile Sharing Issues:**

* Update mobile app to latest version
* Check device permissions for sharing
* Try using browser instead of app (or vice versa)
* Verify internet connection is stable

### Additional Resources

* VIVERSE Support: https://support.viverse.com/
* VIVERSE Worlds: https://worlds.viverse.com/
* VIVERSE Community Guidelines: https://www.viverse.com/guidelines
* Web Share API Documentation: https://developer.mozilla.org/en-US/docs/Web/API/Navigator/share
