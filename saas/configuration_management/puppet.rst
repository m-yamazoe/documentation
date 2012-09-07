.. _saas_puppet:

Puppet
=======

Puppet is a configuration management tool created by PuppetLabs. enStratus *currently*
supports:

* Puppet Enterprise 2.5

We are evaluating the best way to add support for non-PE2.5 Puppet masters.
   
Prerequisites
---------------

There are a few prerequisites required for end-to-end automation using Puppet with
enStratus:

* A working PE2.5 Server
* The enStratus ``espm`` agent installed on the SAME server as PE2.5
* Python 2.6 on the PE2.5 server (for ``espm``)
* A registered image in the cloud with enStratus agent v17 or higher

.. toctree::
   :maxdepth: 1
   :hidden:

   espm
   puppet_account
   puppet_discovery
   puppet_agent
   puppet_launch
