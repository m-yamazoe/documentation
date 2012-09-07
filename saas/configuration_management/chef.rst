.. _saas_chef:

Chef
=====

Chef is a configuration management tool created by Opscode. enStratus supports the three
current types of Chef servers:

* Hosted Chef (OHC)
* Open Source Chef (OSC)
* Private Chef (OPC)

enStratus works with version 0.10.x of these.
   

Prerequisites
---------------

There are a few prerequisites required for end-to-end automation using Chef with enStratus:

* A working Chef server or Opscode Hosted Chef account
* A valid set of credentials to the Chef server
* A registered image in the cloud with enStratus agent v17 or higher

.. toctree::
   :maxdepth: 1
   :hidden:

   chef_credentials
   chef_account
   chef_discovery
   chef_agent
   chef_launch
