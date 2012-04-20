Wordpress Demo
--------------

.. note:: Estimated time to complete this tutorial will vary based on several factors such
   as the speed of the cloud provider, and the skill level of the operator.

   New Guy, no SSH skills, no Linux: 1 mo.

   SSH/Firewall skills, cloud-savvy: 2 hrs, maybe less.

   Cloud god: 1 hr.

Purpose
~~~~~~~
The purpose of this document is to start from scratch and build a deployable enStratus
application architecture including a Wordpress web application backed by a MySQL database.

Prerequisites
~~~~~~~~~~~~~
Users seeking to successfully complete this tutorial must have the following items/skills.

#. A cloud account or cloud accounts connected to enStratus. EC2 is the cloud I will cover
   here.
#. Cloud files (object) storage
#. Familiarity with SSH.
   
   Users will need to be able to use the secure copy program (scp) or an equivalent to
   move the chef cookbook used to prepare the image.

#. Rudimentary familiarity with the Linux operating system command line. However, most of
   the instructions should be copy-and-paste.

Downloads
^^^^^^^^^

Chef solo cookbook: wordpress-demo-prep.tar.gz 
Wordpress service: wordpress.tar.gz
MySQL service: mysql.tar.gz
Datasource: wordpresscontent.sql

Overview
~~~~~~~~

.. note:: Some of the step are purposefully suboptimal to illustrate the process at hand.
   There are usually many ways to approach the processes we're describing, this is just
   one.


Make Image
~~~~~~~~~~
To make an image, start with a generic EC2 image from `Alestic <http://alestic.com/>`_.
Eric's team has recently begun publishing only 64-bit images. Any generic ubuntu image
*should* work, but this tutorial has only been tested with images from Alestic.

1. Start the image as a VM, choose a large product size, not t1.micro for best results.
   Also, generate and use an SSH key during the launch.

   Save the key and chmod it

.. code-block:: bash

   chmod 600 theKey

2. While the image launches, open the firewall so you can access port 22 from your
   location.
3. Once the VM is started, copy the wordpress-demo-prep.tar.gz file to the instance.
   The command to do so will be something of the form:

.. code-block:: bash

   scp -i theKey wordpress-demo-prep.tar.gz ubuntu@ip.of.running.instance:~

4. Next, ssh onto the running instance, and take root:

.. code-block:: bash

   ssh -i theKey ubuntu@ip.of.running.instance

   sudo su

5. Install the chef client:

.. code-block:: bash

   echo "deb http://apt.opscode.com/ `lsb_release -cs`-0.10 main" | sudo tee /etc/apt/sources.list.d/opscode.list
   sudo mkdir -p /etc/apt/trusted.gpg.d
   gpg --keyserver keys.gnupg.net --recv-keys 83EF826A
   gpg --export packages@opscode.com | sudo tee /etc/apt/trusted.gpg.d/opscode-keyring.gpg > /dev/null
   sudo apt-get update
   sudo apt-get -y upgrade
   sudo apt-get -y install chef

6. Extract the wordpress-demo-prep.tar.gz file:

.. code-block:: bash

   tar -xzf wordpress-demo-prep.tar.gz

7. Execute the chef-solo run:

.. code-block:: bash

   chef-solo -j node.json -c solo.rb

During this step, some packages necessary for running a typical LAMP stack application
will be installed, along with the latest enStratus agent. Depending on your connection and
mirror speeds, this may take up to 5-7 minutes.

The purpose of this step is to prepare the image for running PHP and MySQL applications,
not to install the application itself, that comes later durin the launch and orchestration
steps of a deployment launch.

Once this step completes, initiate the build of the machine image from within the
enStratus console.

.. warning:: If the image is not build using the server actions > Make Image menu option
  in the enStratus console, it will not be available for use in the deployment. This measure
  is in place to protect users from attempting to use an image that does not have the agent
  on it for automation.

While the image builds, it's time to upload the service images for use by enStratus.

Upload Services
~~~~~~~~~~~~~~~

Using Automation > Service Images, upload wordpress.tar.gz and mysql.tar.gz as service
image files. enStratus stores these files in cloud files storage and will initiate a
download of these files at launch time by the enStratus agent.

Upload DataSource
~~~~~~~~~~~~~~~~~

Using Automation > Datasource, upload wordpresscontent.tar.gz as datasource files.
enStratus stores datasource files in cloud files storage and will initiate a download of
this file to services that are configured to have datasources.

Configure Deployment
~~~~~~~~~~~~~~~~~~~~
Now that those steps are complete, it's time to start building the application
architecture. First step, create tiers.

Create Tiers
^^^^^^^^^^^^
1. Use the designer diagram to add a tier. Give the tier a high-level generic name like
   Application Tier. Tiers can hold many services, and we'll give a more specific name for
   the services. This tier will house only one service for this tutorial, the wordpress
   application.

Screenshot

2. Use the desginer diagram to add another tier. Give the tier a name like Database Tier.
   This tier will hold the MySQL service.

Screenshot

Add Services
~~~~~~~~~~~~
Adding services to tiers means telling enStratus what service should be installed on
servers running in the tier. This action only needs to be completed once, no matter how
many regions/clouds the tier spans.

To add a service to a tier, select the tier by clicking on it in the designer diagram and
choose +add service. enStratus will present the list of the services available for
attaching to the tier. This list should have at a minimum the two services uploaded above. 

.. note:: It is also possible to tell enStratus to use a previously generated backup as a
  service. Pretty cool.

Associate the wordpress service with the application tier and the mysql service with the
database tier.

Screenshot

Configure Services
~~~~~~~~~~~~~~~~~~
Next, it's time to configure the services. Configuring services means telling enStratus
what the relationship is, if any, between the services and what information should be
passed to dynamically configure the service at run time.

Ports
^^^^^
The ports option in the actions menu for services allows the application designer to
specify ports information that will be passed to the service at run time.

For the wordpress service, make ports setting as shown:

Screenshot

For the MySQL service, make the ports setting as shown:

Screenshot

Data Source
^^^^^^^^^^^
The Datasource options in actions menu for services allows the application designer to
associate a starting datasource with a database service. On the MySQL service, associate
the datasource uploaded earlier as shown:

Screenshot

Set Dependencies
~~~~~~~~~~~~~~~~
This step is critical. Dependencies let enStratus know how to *orchestrate* the deployment
launch and service configuration.

For this tutorial, we're going to set a dependency for the wordpress service. It's going
to depend on the *data source* installed as part of the MySQL service. What this means is
that at run time, enStratus will ensure:

1. The MySQL service is installed and successfully configured
2. The datasource is successfully installed on the MySQL service.

and then, and only then will

3. The application service be installed, since it *depends* on steps 1 and 2.

Screenshot(s)

Configure Launch Configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The next and final thing to do is to 


Set Scaling Rules
~~~~~~~~~~~~~~~~~

Launch Deployment
~~~~~~~~~~~~~~~~~
