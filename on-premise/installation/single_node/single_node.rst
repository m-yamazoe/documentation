.. _single_node_install:

Single Node
-----------

This is the enStratus on-premise install built using Opscode chef-solo

.. warning:: 

   By default, this repository will work **OUT OF THE BOX** to install a standalone server
   hosting all the enStratus components.  This is by design as the initial and primary use
   case is for testing and POC.

   This installer **CAN** be used to install multi-node production installs but it
   requires more manual configuration.

Components
~~~~~~~~~~

enStratus software can be installed on a single server, combining all software components:

1. Dispatcher

  * Provisioning Database (MySQL)
  * Analytics Database (MySQL)

2. Workers
3. Monitors
4. Key Manager

  * Credentials Database (MySQL)

5. Riak
6. Rabbit MQ

7. Console

  * Console Database (MySQL)

8. API
9. Console Worker
10. LDAP


System Requirements
~~~~~~~~~~~~~~~~~~~

Provision a server (can be virtual, we often test using an m1.large instance in EC2) for
the installation. Installation can also be done using local virtualization. These specs
are for a single node installation

Minimum Requirements
~~~~~~~~~~~~~~~~~~~~

The bare minimum for virtualized installation is:

* 2 VCPU
* 8GB of memory
* 15GB of disk (1GB is needed for the caching of enStratus software during installation. Another 1GB is needed for the final install)
* 64-bit Ubuntu 10.04, 12.04, CentOS 5

Recommended Requirements
~~~~~~~~~~~~~~~~~~~~~~~~

* 4 VCPU
* 15GB of memory
* 60GB of storage (this is primarly logging and data)
* 64-bit Ubuntu 12.04

External connectivity
~~~~~~~~~~~~~~~~~~~~~

**This installation requires external Internet connectivity.**

The installer pulls all requirements from the internet, including vendor OS repositories.
You only need to download the packages once. They will be cached the first run in the
`cache` directory.  

Chef will reuse those packages if the checksums match the defined
ones.

Overview
~~~~~~~~

The installation procedure will move through the following steps:

#. Extract chef-solo package

#. Download and prepare the JDK and JCE

#. Install enStratus

Installation
~~~~~~~~~~~~

The following assumes competence with Linux, and command line utilities like ssh. If you
don't use these utilities on a near daily basis, this installer is probably not for you.

Extract Chef-Solo Package
~~~~~~~~~~~~~~~~~~~~~~~~~

The installation chef-solo package will be provided to you by an enStratus engineer.
Extract it.

The contents of the directory will be similar to:

.. hlist::
   :columns: 3

   * README.md
   * TODO.md
   * backup
   * cache
   * checksums
   * classes
   * cookbooks
   * docs
   * enstratus-utilities.jar
   * json_templates
   * local_settings
   * roles
   * setup.sh
   * solo.rb

Prepare the JDK and the JCE
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Unfortunately, there is NO way to automatically install the JDK and JCE legally. This
means that it is necessary to do some preparation work.

enStratus will not operate without the Java 6 JDK and the unlimited strength encryption provided for by the JCE library.

You will need to download the java 6 JDK:

`JDK Download Page <http://www.oracle.com/technetwork/java/javase/downloads/jdk6-downloads-1637591.html>`_

You will also need to get the JCE:

`JCE Download Page <http://www.oracle.com/technetwork/java/javase/downloads/jce-6-download-429243.html>`_

Extract the jdk, so you get something like jdk1.6.0_33 as a directory. Rename (read: `mv` ) it: 

.. code-block:: bash

   mv jdk1.6.0_33 jdk

Tar that directory into cookbooks/enstratus/files/default/jdk.tar.gz

.. code-block:: bash

   tar -czf cookbooks/enstratus/files/default/jdk.tar.gz jdk

Move the jce directory: cookbooks/enstratus/files/default/jce

.. code-block:: bash

   mv jce cookbooks/enstratus/files/default/

Running the setup
~~~~~~~~~~~~~~~~~

The setup script is designed to work out of the box with the single-node
installation. There is a `setup.sh` script provided that will do configuration for
you. At a minimum, `setup.sh` needs two settings passed to it:

#. Your license key 
#. Download password for the enStratus software. 

These should have been provided to you by enStratus.

Optionally, you can specify a `savedir` where you would like to save your settings.

Help output
^^^^^^^^^^^

.. code-block:: bash

   Usage: setup.sh [-h] [-e] -p <download password> -l <license key> [-s savename] [-c <console hostname>] 
   [-n <number of nodes>] [-m <mapping string>] [-a <optional sourceCidr string>]

   -p: The password for downloading enStratus
   -l: The license key for enStratus
   
   For most single node installations, specify the download password and license key.
   
   optional arguments
   ------------------
   -h: This text
   -e: extended help
   -c: Alternate hostname to use for the console. [e.g. cloud.mycompany.com] (default: fqdn
   of console node)
   -a: Alternate string to use for the sourceCidr entry. You know if you need this.
   -s: A name to identify this installation
   -n: Number of nodes in installation [1,2,4] (default: 1)
   -m: Mapping string [e.g. frontend:192.168.1.1,backend:backend.mydomain.com]

For a single node, most users should run something similar to

.. code-block:: bash

  ./setup -p <the_password_here> -l <license_key_here> -c cloud.mycompany.com


Running without a savedir
^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

   root@host# ./setup.sh -l XXXX -p YYYYY
   Savedir not specified. Using temporary directory
   ## CHECKING JDK and JCE setup under /tmp/es-onpremise-chef-solo/cookbooks/enstratus/files/default
   ## EXTRACTING temporary JDK - from /tmp/es-onpremise-chef-solo/cookbooks/enstratus/files/default/jdk.tar.gz to /tmp
   ## COPYING JCE jars to temporary JDK
   Generating Keys
   Creating local_settings//tmp/tmp.KZ1vPP28lG/genkeys.txt file
   Writing JSON files to 'local_settings//tmp/tmp.KZ1vPP28lG/'
   #Ready to run :
   #
   chef-solo -j local_settings//tmp/tmp.KZ1vPP28lG/single_node.json -c solo.rb

Running with a savedir
^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

   root@host# ./setup.sh -s my_local_install -l XXXX -p YYYYY
   Savedir my_local_install not found. Assuming new run...
   
   ## CHECKING JDK and JCE setup under /tmp/es-onpremise-chef-solo/cookbooks/enstratus/files/default
   ## EXTRACTING temporary JDK - from /tmp/es-onpremise-chef-solo/cookbooks/enstratus/files/default/jdk.tar.gz to /tmp
   ## COPYING JCE jars to temporary JDK
   Generating Keys
   Creating local_settings/my_local_install/genkeys.txt file
   Writing JSON files to 'local_settings/my_local_install/'
   #Ready to run :
   #
   chef-solo -j local_settings/my_local_install/single_node.json -c solo.rb

Running the install with a previous savedir
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

   root@host# ./setup.sh -s my_local_install -l XXXX -p YYYYY
   Savedir my_local_install found..
   
   Existing config in use. Skipping password generation
   ## CHECKING JDK and JCE setup under /tmp/es-onpremise-chef-solo/cookbooks/enstratus/files/default
   ## EXTRACTING temporary JDK - from /tmp/es-onpremise-chef-solo/cookbooks/enstratus/files/default/jdk.tar.gz to /tmp
   ## COPYING JCE jars to temporary JDK
   Reading existing keys from ./local_settings/my_local_install/
   Writing JSON files to 'local_settings/my_local_install/'
   #Ready to run :
   #
   chef-solo -j local_settings/my_local_install/single_node.json -c solo.rb

This is CRITICAL if you want to be able to rerun the installation on the same machine.
The installer uses chef-solo. Chef-solo does not persist any state between invocations in
the same way that chef with a Chef server does. The `setup.sh` script is designed to allow
you to persist that state between runs.
