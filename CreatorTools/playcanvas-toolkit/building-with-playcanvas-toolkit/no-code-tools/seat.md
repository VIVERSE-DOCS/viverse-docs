---
description: >-
  This document provides a guide that can be used to add a seat to objects in
  VIVERSE project. This allows the avatar to sit down.
---

# Seat

***

### Seat

#### Add functionality to allow avatar to sit down

| <img src="../../../.gitbook/assets/image (544).png" alt="" data-size="original">                                            | <img src="../../../.gitbook/assets/image (545).png" alt="" data-size="original">                                          |
| --------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| When the avatar enters the **SeatHintFarAwayTriggerSphere**, the **SeatHintFarAway** icon (white dot) shows above the seat. | When the avatar enters the **SeatHintTriggerSphere**, the **SeatHint** button (sitting person icon) shows above the seat. |
| <img src="../../../.gitbook/assets/image (554).png" alt="" data-size="original">                                            |                                                                                                                           |
| When the avatar clicks on the **SeatHint** button, the avatar sits down.                                                    |                                                                                                                           |

{% stepper %}
{% step %}
#### Add the chair&#x20;

A. In this example, the chairs and table have been created in the scene under a single entity.

B. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (547).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### Add the Seat module

A. In the VIVERSE extension, select the **Seat** plugin for the **Select plugins** dropdown.

B. Select the **Seat** module and add it.

C. Add a value to the **number of seats** field.

<figure><img src="../../../.gitbook/assets/image (548).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### Adjust the sitting boundary

A. The **Seat** is automatically generated.

B. Adjust the **Radius** on the **Collision** component to modify the sitting boundary.

<figure><img src="../../../.gitbook/assets/image (549).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### View the SeatHintFarAway icon

A. The **SeatHintFarAway** icon is a white dot that is automatically generated and hovers above each seat.

<figure><img src="../../../.gitbook/assets/image (550).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### Adjust the boundary for the SeatHintFarAway icon

A. The **SeatHintFarAwayTriggerSphere** is automatically generated.

B. Adjust the **Radius** on the **Collision** component to modify the distance away before the **SeatHintFarAway** icon displays. Also ensure the height of the collider is above ground level, so the player capsule collider is sure to intersect with it.

<figure><img src="../../../.gitbook/assets/image (551).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### View the SeatHint button

A. The **SeatHint** button has an icon of a sitting person. The button is automatically generated and hovers above each seat. When clicked, the avatar will sit.

<figure><img src="../../../.gitbook/assets/image (552).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### Adjust the boundary for the SeatHint button

A. The **SeatHintTriggerSphere** is automatically generated.

B. Adjust the **Radius** on the **Collision** component to modify the distance away before the **SeatHint** icon displays.

<figure><img src="../../../.gitbook/assets/image (553).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}
