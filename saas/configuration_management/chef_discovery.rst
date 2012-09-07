.. _saas_chef_console_discovery:

Checking the status of discovery
--------------------------------

Once you've registered your account with enStratus, a background thread will undertake the
task of enumerating the following information from your Chef server:

* environments
* roles
* cookbooks (and recipes)

Currently there is no visible progress on this action for the user. To check the progress
of discovery, do the following:

* Navigate to "Configuration Management" -> "Components"

.. figure:: ./images/cm-menu-components.png
   :alt: Configuration Management Menu
   :align: center

* Select your Chef server from the drop-down titled "Select Configuration Management Account"

.. figure:: ./images/cm-components-account-menu.png
   :alt: Components Account Menu
   :align: center

The page will redraw and display a list of any ``roles`` and ``cookbooks`` discovered
along with the date and time of discovery

.. figure:: ./images/cm-components-page.png
   :height: 700 px
   :width: 1600 px
   :alt: Components Account Menu
   :align: center
   :scale: 50%

Asynchronous Discovery
~~~~~~~~~~~~~~~~~~~~~~

.. warning:: Because discovery is asynchronous, you might not immediately see all the data
   you expect to see.  As the page says, changes on the Chef server may not be immediately
   reflected.  Currently there is no way to force a refresh of the discovery process.

Note that the asynchronous nature ONLY affects the ability of enStratus to know what objects are available for assignment when launching a server. enStratus discovers names of cookbooks and the names of the recipes they contain, but does not discover the content. If you make a change to the content of an existing cookbook, recipe or role that enStratus has discovered, you do not need to wait for enStratus to rediscover that cookbook, role or recipe. However if you add a NEW recipe to a cookbook, it will not be immediately visible in enStratus. The same applies for deleting a recipe from a cookbook.

This will become clearer when you launch a server the first time.

.. tip:: When making frequent changes to your Chef cookbooks, it can be difficult to test those
   especially when creating new roles and they have yet to be discovered by enStratus (and
   thus are not visible in the ``Launch`` dialog for new instances). To work around this
   limitation, you can create a few empty test roles with no run_list in them before
   registering the account with enStratus. Now you can change the run_list of those roles at
   will on the Chef server without enStratus needing to know anything more than they exist.
