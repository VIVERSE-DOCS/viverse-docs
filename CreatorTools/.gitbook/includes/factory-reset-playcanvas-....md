---
title: '"Factory Reset" PlayCanvas ...'
---

# "Factory Reset" PlayCanvas Extension

> Sometimes issues in the installation process can produce persistent bugs that are only fixed by fully resetting the Extension. If you're experiencing these bugs please follow the steps below

{% stepper %}
{% step %}
### Sign in and out of VIVERSE

Sign out of your account on viverse.com and then sign back in. **MAKE SURE** you are using the same email account as the one associated with your PlayCanvas account
{% endstep %}

{% step %}
### Delete VIVERSE entities from PlayCanvas project

Go back to your PlayCanvas project and delete:

* The `Extension` Entity from your scene hierarchy
* The `.viverse` folder from your project assets
* The `.index.mjs` folder from your scripts folder
{% endstep %}

{% step %}
### Clear Application Storage

Open the [Developer Tools](https://elfsight.com/blog/how-to-work-with-developer-console/) in your Chrome broswer, go to "Application", select "Storage" in the left hand toolbar and click the "Delete Site Data" button
{% endstep %}

{% step %}
### Refresh the page

Refresh your PlayCanvas project page with the VIVERSE extension enabled
{% endstep %}

{% step %}
### Sign in back to PlayCanvas

Sign in to PlayCanvas when prompted. **MAKE SURE** you are using the same email account as the one associated with your VIVERSE account
{% endstep %}

{% step %}
### Sign in back to VIVERSE

Sign in to VIVERSE from within your PlayCanvas project using the VIVERSE Scene Settings
{% endstep %}
{% endstepper %}
