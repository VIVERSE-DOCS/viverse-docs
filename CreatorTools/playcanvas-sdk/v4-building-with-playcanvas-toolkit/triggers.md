# Triggers

***

## Reference

What is the role of Triggers in VIVERSE Framework?

## Trigger Types

***

{% columns %}
{% column width="25%" %}
`OnSelect`<br>
{% endcolumn %}

{% column width="50%" %}
Activates when the Player points and clicks on this Trigger. Requires **Collision Component** to be attached to the Entity
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/tr01.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`OnCollision`<br>
{% endcolumn %}

{% column width="50%" %}
Activates when another **Rigidbody** interacts with this Trigger. Supported interactions:

* `TriggerEnter` / `TriggerLeave` \
  Requires **Collision Component** to be attached
* `CollisionStart` / `CollisionEnd` \
  Requires both **Collision** and **Rigidbody Components**

You can allow interactions only with the Player or some Entity with specific Tag. See **Collision Filter** for available options
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/tr02.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/tr03b (2).png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`OnAnimation`<br>
{% endcolumn %}

{% column width="50%" %}
Activates when animation state changes for this Trigger entity. Requires **Animation Component** to be attached. Supported animation events:

* `Start` / `End` / `CustomEvent`
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/tr03.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`On`\
`SeatState`\
`Changed`<br>
{% endcolumn %}

{% column width="50%" %}
Activates when this Seat Entity changes its state. Works in tandem with **Seat Component**, therefore requires **viverseSeat** script to be attached to this Entity. Supported events:

* `Occupied` / `Vacated`
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/tr04.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`On`\
`Notification`\
`Event`<br>
{% endcolumn %}

{% column width="50%" %}
Activates when another Entity with **Action Component** fires `PublishNotification` event with particular `Event Name` matching the one in this Trigger. You can use it to send / receive custom Notification Events between different Entities in your Scene
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/tr06.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

{% columns %}
{% column width="25%" %}
`On`\
`Action`\
`Executing`<br>
{% endcolumn %}

{% column width="50%" %}
Some description
{% endcolumn %}

{% column width="24.999999999999986%" %}
<figure><img src="../../.gitbook/assets/tr03.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

***

## Examples

\[...]





