.. _saas_chef:

Chef
=====
Chef is a configuration management tool created by Opscode. enStratus supports the three current types of Chef servers:

* Hosted Chef (OHC)
* Open Source Chef (OSC)
* Private Chef (OPC)

Currently enStratus works with version 0.10.x of these.
   

Prerequisites
---------------
There are a few prerequisites required for end-to-end automation using Chef with enStratus:

* A working Chef server or Opscode Hosted Chef account (This step is up to you)
* A valid set of credentials to the Chef server
* A registered image in the cloud with enStratus agent v17 or higher

The following steps will walk you through getting enStratus connected to your Chef server:

.. toctree::
   :maxdepth: 1

   Step 1 - Generating credentials <chef_credentials>
   Step 2 - Adding to enStratus <chef_account>
   Step 3 - Checking discovery <chef_discovery>
   Step 4 - Images and Agents <chef_agent>
   Step 5 - Launching an Instance <chef_launch>

