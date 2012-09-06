.. _saas_chef_console_account:

Adding to enStratus
~~~~~~~~~~~~~~~~~~~~
Once you've created an account for enStratus to use and have the credentials, you can add that to the enStratus console:

* Navigate to "Configuration Managemet" -> "Accounts"

.. figure:: ./images/cm-menu.png
   :alt: Configuration Management Menu
   :align: center

* Click the link on the right side to "Add A New Configuration Management Account"

.. figure:: ./images/add-new-cm-account.png
   :alt: Configuration Management Menu
   :align: center

* Select "Chef" from the "Configuration Management System" drop-down menu
* Fill in the fields as described.
	Note that speficically to enStratus, the following fields are required:
	* Budget Codes
	* Name
	* Description

.. figure:: ./images/add-new-chef-account.png
   :alt: Configuration Management Menu
   :align: center

* Click "Save"
 
At this point, enStratus will now begin discovery of your ``roles``, ``cookbooks`` and ``environments``.