.. _saas_puppet:

Puppet
=======
Chef is a configuration management tool created by PuppetLabs. enStratus *currently* supports:

* Puppet Enterprise 2.5

We are evaluating the best way to add support for non-PE2.5 Puppet masters.
   

Pre-requisites
---------------
There are a few prerequisites required for end-to-end automation using Puppet with enStratus:

* A working PE2.5 Server (This step is up to you)
* The enStratus ``espm`` agent installed on the SAME server as PE2.5
* Python 2.6 on the PE2.5 server (for ``espm``)
* A registered image in the cloud with enStratus agent v17 or higher

The following steps will walk you through getting enStratus connected to your Puppet server:

.. toctree::
   :maxdepth: 1

   Step 1 - Installing ESPM <espm>
   Step 2 - Adding to enStratus <puppet_account>
   Step 3 - Checking discovery <puppet_discovery>
   Step 4 - Images and Agents <puppet_agent>
   Step 5 - Launching an Instance <puppet_launch>