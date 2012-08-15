.. _acct_notifications:

Setting Account Alert and Notification Preferences
--------------------------------------------------

.. Alert and notification preferences can also be set account-wide by administrators by going to Account Settings > Account Notifications.

Once targets are set, you can specify which alerts and notifications will trigger sending to those targets. 
These options can be set account-wide by administrators by going to Account Settings > Account Notifications (directly beneath Alert and
Notification Targets).


Alert Preferences
~~~~~~~~~~~~~~~~~

Alerts are triggered by any alarm event (e.g. unexpected server termination, backup failure, or intrusion detection).

You can choose the minimum severity of the alerts you receive and how you want to receive them (targets set in previous step).

.. figure:: ./images/notifications_3.png
   :alt: Alert Preferences
   :align: center


Notification Preferences
~~~~~~~~~~~~~~~~~~~~~~~~

enStratus allows granular control when it comes to notifications triggered by state change events. You choose a resource type (Any Resource, Server, Firewall, IP Address), a state change type (Create, Update, Delete), and the delivery method(s).

.. figure:: ./images/notifications_4.png
   :alt: Notification Preferences
   :align: center


If you choose identical delivery targets for Create, Update, and Delete on the same resource, those preferences will be consolidated. Otherwise notification settings are additive.

Consider the following preferences:

.. figure:: ./images/notifications_5.png
   :alt: Notification Preferences
   :align: center

|

In this example, a create event on any resource (ANY.CREATE) will trigger a notification to the Topic target. This includes Servers, Firewalls, and IP Addresses.
An update event on a firewall (FIREWALL.UPDATE) will trigger an SMS notification.
Any kind of event on a server (SERVER.ANY) will trigger both Email and Topic notifications.
Updating or deleting an IP address will trigger NO notifications. 
Deleting a firewall will also trigger no notifications.
