.. _single_node_install:

Single Node
-----------

.. warning:: These instructions should be used for POC/testing only.

.. note:: These installation instructions have only been tested on the Linux operating
   system, Ubuntu 10.04/12.04 LTS x86_64 release. 

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

Overview
~~~~~~~~

For a single node installation, enStratus leverages the power of chef-solo to satisfy
the software prerequisites. Once the prerequisite software is installed, installing
enStratus software can proceed.

System Requirements
~~~~~~~~~~~~~~~~~~~

Provision a server (can be virtual, we often test using an m1.large instance in EC2) for
the installation.

Recommended Operating Specifications
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

CPU: 4

Memory: 15 Gb

Storage: 60 Gb

Architecture: 64-bit

Again, an m1.xlarge will fill these requirements quite well. You can probably get by with
an m1.large to save on costs.

.. note:: This installation requires external Internet connectivity.

Recommended Images
^^^^^^^^^^^^^^^^^^

Start with a generic EC2 image from `Alestic <http://alestic.com/>`_ or the equivalent in
your environment. 

Installation Procedure
~~~~~~~~~~~~~~~~~~~~~~

The installation procedure will move through the following steps:

#. Install Chef client

#. Extract chef-solo package

#. Download and prepare the JDK and JCE

#. Edit installation attributes

#. Install enStratus

Installation Steps
~~~~~~~~~~~~~~~~~~

Install Chef client
^^^^^^^^^^^^^^^^^^^

To install the chef client, use the following steps:

1. apt-get update && apt-get -y upgrade
2. curl -L http://www.opscode.com/chef/install.sh | sudo bash
3. apt-get -y install libmysqlclient-dev 
4. /opt/chef/embedded/bin/gem install mysql

   Be sure you're installing from home/ubuntu, or edit solo.rb accordingly.

Extract chef-solo package
^^^^^^^^^^^^^^^^^^^^^^^^^

The installation chef-solo package will be provided to you by an enStratus engineer.
Extract it. You will see something like this.

.. code-block:: bash

   [root@localhost es-onpremise-chef-solo]# ls 
   classes  cookbooks  enstratus-utilities.jar README.md  roles  single_node.json  solo.rb

Download and Prepare the JDK and JCE
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

enStratus will not operate without the Java 6 JDK and the unlimited strength encryption
provided for by the JCE library.

You will need to download the java 6 JDK:

`JDK Download Page <http://www.oracle.com/technetwork/java/javase/downloads/jdk6-downloads-1637591.html>`_

You will also need to get the JCE:

`JCE Download Page <http://www.oracle.com/technetwork/java/javase/downloads/jce-6-download-429243.html>`_

Extract the jdk, so you get some thing like jdk1.6.0_33 as a directory. Rename (read: `mv` ) it: 

.. code-block:: bash

    mv jdk1.6.0_33 jdk

Tar that directory into cookbooks/enstratus/files/default/jdk.tar.gz

.. code-block:: bash

    tar -czf jdk.tar.gz jdk
    mv jdk.tar.gz cookbooks/enstratus/files/default/

Move the jce directory: cookbooks/enstratus/files/default/jce

.. code-block:: bash

    mv jce cookbooks/enstratus/files/default/


Edit Installation Attributes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Edit the file:

`cookbooks/enstratus/files/default/.bashrc and cookbooks/enstratus/attributes/default.rb`

    Change console_url to what you want. This will be the url you use to access the
    enStratus console. Example: cloud.mycompany.com

.. note:: In most cases, you'll have to make a hosts file entry for this url.

Change console_ip to what you want.

This value must be accessible to the console user. If you're installing in EC2, you most
probably want to use the publicly addressable IP address. 

.. note:: You'll need to open the firewall (security group) on port 443 to access the
   console.

Change source_cidr to what you want. The source_cidr attribute should usually be set to
the public IP address of the server. 

As part of the installation process, you will have received a directory called `classes`
and a file called `enstratus-utilities.jar`.

.. note:: This command will only run well on a system with java installed. So we have a
   chicken-and-egg problem here. The chef-solo will help install java, but the installer
   needs this information to proceed. Luckily, this command can be run on any machine with
   Java and JCE installed.

Run the command:

.. code-block:: bash

    java -cp enstratus-utilities.jar:./classes/ net.enstratus.deploy.GenerateKeys

You will get output like:

.. code-block:: text

    dispatcherEncryptionKey=b%2MKnlmqVGIlGA6e%3T#QdYvxR&A0PeIC
    accessKey=lk*zJgL&BJTAm$7j!TVb#AL6Hbhq5$
    encryptedManagementKey=bd75e62e61c158f4df10a5d6448978d800067ab5dd1ade8d63528f53ea3b15e770ebb25331430114a1bb72663a6b03c5d55dc911c328d7f435270bcef52936f7
    firstEncryptedAccessKey=3f7c501c59879aaa4631927bd164ffc64dc34b75bfe5f7f0a202f91533cc4495
    consoleEncryptionKey=w!h!WTa^Qu85cwD&NE[xsv#&BuikwL6R2-N_bNSOpAIY(
    secondEncryptedAccessKey=890e1013971b6fa826d37c2e910e79d014e620004931cabf4a09e3d73e8c09c6

Or, you can use the ones right here, but it's best to generate your own, since anyone with
these keys could potentially access your customer data.

Use these values to fill in the attributes in cookbooks/enstratus/attributes/default.rb:

.. code-block:: bash

    default[:enstratus][:dispatcherEncryptionKey] = ''
    default[:enstratus][:accessKey] = ''
    default[:enstratus][:encryptedManagementKey] = ''
    default[:enstratus][:firstEncryptedAccessKey] = ''
    default[:enstratus][:consoleEncryptionKey] = ''
    default[:enstratus][:secondEncryptedAccessKey] = ''

.. note:: The mysql root user password is set in the server attributes of the mysql
   cookbook cookbooks/mysql/attributes/server.rb:

default['mysql']['server_root_password']

The value in cookbooks/enstratus/files/default/.bashrc and cookbooks/enstratus/attributes/default.rb must also match.

**Attributes Summary**

Before initiating the installation, make sure you have the following attributes set:

.. code-block:: bash

   default[:enstratus][:download][:analytics_schema] = ''
   default[:enstratus][:download][:console_service] = ''
   default[:enstratus][:download][:api_service] = ''
   default[:enstratus][:download][:console_schema] = ''
   default[:enstratus][:download][:credentials_schema] = ''
   default[:enstratus][:download][:dispatcher_service] = ''
   default[:enstratus][:download][:enstratus_console] = ''
   default[:enstratus][:download][:km_service] = ''
   default[:enstratus][:download][:monitor_service] = ''
   default[:enstratus][:download][:provisioning_schema] = ''
   default[:enstratus][:download][:worker_service] = ''

An enStratus engineer will provide these attributes along with the license key.

Choose sensible values here that are appropriate for your environment.

.. code-block:: bash

   default[:enstratus][:license_key] = ''
   
   default[:enstratus][:console_url] = 'cloud.mycompany.com'
   default[:enstratus][:console_ip] = ''
   default[:enstratus][:source_cidr] = ''

These following values come from running:

.. code-block:: bash

   java -cp enstratus-utilities.jar:./classes/ net.enstratus.deploy.GenerateKeys

   default[:enstratus][:dispatcherEncryptionKey] = ''
   default[:enstratus][:accessKey] = ''
   default[:enstratus][:encryptedManagementKey] = ''
   default[:enstratus][:firstEncryptedAccessKey] = ''
   default[:enstratus][:consoleEncryptionKey] = ''
   default[:enstratus][:secondEncryptedAccessKey] = ''

Edit these only if you know what you're doing.

.. code-block:: bash

   default[:enstratus][:mysql_admin] = 'root'
   default[:enstratus][:mysql_password] = 'ooYGsdrDOTk814HsXFMgQw'
   default[:enstratus][:riak_host] = 'localhost'
   default[:enstratus][:riak_port] = '8098'
   default[:enstratus][:mq_user] = 'qsmq'
   default[:enstratus][:mq_password] = 'enstratus'
   default[:enstratus][:mq_host] = 'localhost'
   default[:enstratus][:mq_port] = '5672'
   default[:enstratus][:dispatcher_hostname] = 'dispatcher'
   default[:enstratus][:km_hostname] = 'km'
   default[:enstratus][:java_home] = '/usr/local/jdk'

Install enStratus
^^^^^^^^^^^^^^^^^

Finally, it's time to install the enStratus software. As root:

.. code-block:: bash

   chef-solo -j single_node.json -c solo.rb
