.. _saas_puppet_agent:

Prepping an image
==================
To be able to launch an instance with Puppet (or any CM for that matter), you must meet the following criteria:

* Your image has v17 of the enStratus agent
* Your image shows as "registered" in the enStratus Console under "Machine Images" (has the enStratus logo)
* Your image has PE2.5 client preinstalled with the appropriate templated configuration files

Depending on your cloud provider and other factors (such as region), enStratus may have already made an image publicly available with the agent preinstalled.

.. note::
	There is an entire guide dedicated to the enStatus agent, however there are a few bits of information worth recapping here specifically as it relates to interaction with Puppet.

Differences from manual provisioning
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Simply put, enStratus does not use SSH to interact with servers. All communication (outside of the initial 'phone-home') is driven from enStratus to launched instances via the enStratus agent.

The enStratus agent is a java application that is built around a series of extensible shell scripts. This has its benefits in that what the agent does, can be customized by the user.

In the case of a freshly launched instance, once it has sent its "alive" packet back to enStratus provisioning, enStratus will, via the agent, run the following script:

``/enstratus/bin/runConfigurationManagement-PUPPET``

This script will get information passed to it via the enStratus agent about your Puppet account as well as your pregenerated and signed client certificates. By default, this script will perform the following actions:

* Inspect the data passed down from enStratus about the Puppet master. If the value is an IP address, a hosts file entry will be created pointing the names ``puppet`` and ``puppetmaster`` to that IP address. This is beneficial when you don't yet have a DNS entry pointing to your Puppet server.

At this point the agent expects to find the following files and directories on the filesystem:

* /etc/puppetlabs/puppet/puppet.conf
* /etc/puppetlabs/puppet/ssl/certs/
* /etc/puppetlabs/puppet/ssl/private_keys/

It expects the puppet.conf file to look like so:

::

   [main]
       vardir = /var/opt/lib/pe-puppet
       logdir = /var/log/pe-puppet
       rundir = /var/run/pe-puppet
       modulepath = /etc/puppetlabs/puppet/modules:/opt/puppet/share/puppet/modules
       user = pe-puppet
       group = pe-puppet
       archive_files = true
       archive_file_server = ES_PUPPET_MASTER

   [agent]
       certname = ES_NODE_NAME
       server = ES_PUPPET_MASTER
       report = true
       classfile = $vardir/classes.txt
       localconfig = $vardir/localconfig
       graph = true
       pluginsync = true

Technically the only critical values are the templated ones for ``ES_NODE_NAME`` and ``ES_PUPPET_MASTER``. Those will be replaced with the name assigned in the enStratus console and the host portion of the value you entered for the Puppet URL when adding the configuration account. If the host portion was an IP address, this will be set to ``puppet`` and, as mentioned previously, a hosts file entry will be created to support that.

* The pregenerated client certificate and key will be copied into the appropriate directories.

Finally the puppet client will be run with the following invocation:

``sudo puppet agent --onetime --no-daemonize --detailed-exitcodes --logdest=/mnt/tmp/es-puppet-firstrun.log``

.. note:: Detailed exit codes are used due to the fact that the launched instance may be part of an enStratus deployment. The assumption is that if any part of the catalog fails to apply, the system is likely not in a state to serve its purpose. For that reason, any exit code of ``4``, ``6`` or ``1`` will be considered a failure to configure.

.. note:: enStratus does not set up any cron jobs or run ``puppet agent`` in daemon mode. This is a site-specific setting and should be managed in your Puppet modules. enStratus is only concerned about the initial bootstrap at this point. enStratus will never initiate a puppet run outside of this initial bootstrap except when used in Deployments.

It's worth noting here that enStratus has removed the certificate signing step entirely. Since we pregenerate and sign the certificates BEFORE we launch the instance, the initial run will not be blocked waiting on someone to sign the certificates nor will you have to turn on autosigning or use wildcards.

When terminating a server in enStratus, it will also make a call back to the ``espm`` agent to delete the node from the ENC as well as revoke its certificates.

Customizing the bootstrap
~~~~~~~~~~~~~~~~~~~~~~~~~~
You can customize the ``/enstratus/bin/runConfigurationManagement-PUPPET`` script as needed. enStratus ships "opinionated" scripts but you can customize them as you see fit. enStratus only tests with the shipped scripts.

Making an Image available
~~~~~~~~~~~~~~~~~~~~~~~~~~
As stated, all interaction with instances from enStratus is via the agent. Because of this, enStratus needs guarantees that the image can be trusted to have the Agent installed.
To this end, there's a process that must be used:

Launch any public or enStratus public machine image
````````````````````````````````````````````````````
As stated, enStratus has been making updated images available with v17 of the agent installed. You are also free to install the agent yourself.

Regardless of which image you launch (public, enstratus or preexisting), the image will be untrusted. To create a "registered" image, you must image a running server from within enStratus. Depending on the cloud provider and the type of imaging (i.e. EBS root vs. instance storage), enStratus will perform the imaging process on any running instance that it believes has the agent installed. Let's use the following screenshots as a guide:

* Navigate to "Compute" and "Machine Images" from the menu
* Search for public images with ``enstratus17`` in the name

.. figure:: ./images/public-ami-search.png
   :alt: Public AMI Search Menu
   :align: center
   :scale: 10 %

The image we'll be using for this document is ``ami-bd3c8ad4`` in AWS US-East and is called ``enStratus17-Ubuntu1004-64-2012090502``. It is an Ubuntu 10.04 64-bit image. It also has Chef 0.10 installed from the Opscode "omnibus" installer.

* Launch the image

Click on the "action" menu for the image and select "Launch"

.. figure:: ./images/launch-image.png
   :alt: Launch Menu
   :align: center
   :scale: 10 %


You'll need to fill in the information as appropriate. For now, do NOT set anything in the "Configuration Management" tab. If you plan on customizing the instance at all before imaging, you'll want to launch it with an SSH keypair configured.

.. figure:: ./images/base-launch.png
   :alt: Launch Screen
   :align: center
   :scale: 10 %


* Customize and make a new image

Once the instance is fully online (``Running`` in the server list) 

.. figure:: ./images/running-base.png
   :alt: Running Base Image
   :align: left
   :scale: 10 %

and has detected the Agent is installed (Agent iconography), you can select ``Make Image`` from the instance's "actions" menu: 

.. figure:: ./images/make-image-menu.png
   :alt: Make Image
   :align: center
   :scale: 10 %


* Make note of the name you give the new image:

.. figure:: ./images/create-image-screen.png
   :alt: Create Image Screen
   :align: center
   :scale: 10 %

As this is an instance store instance, the appropriate ``ec2-bundle-*`` and ``ec2-upload-*`` will be run, via the Agent, on the instance. If this were an EBS volume, the instance would be paused and the root EBS volume snapshotted.

Once the image process is complete, the image will be eventually available under "Compute" -> "Machine Images" with the enStratus logo visible next to it:

.. figure:: ./images/registered-image.png
   :alt: Registered Image
   :align: center
   :scale: 10 %

.. note:: enStratus will add any public image you launch to your own list of machine images.


