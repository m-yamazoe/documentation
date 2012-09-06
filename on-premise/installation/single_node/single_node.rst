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

Installation Overview
~~~~~~~~~~~~~~~~~~~~~

For a single node installation, enStratus leverages the power of chef-solo to satisfy
the software prerequisites. Once the prerequisite software is installed, installing
enStratus software can proceed.

The installation procedure will move through the following steps:

#. Install Chef client

#. Extract chef-solo package

#. Download and prepare the JDK and JCE

#. Generate keys 

#. Edit installation attributes

#. Install enStratus


System Requirements
~~~~~~~~~~~~~~~~~~~

Provision a server (can be virtual, we often test using an m1.large instance in EC2) for
the installation.

**Recommended Operating Specifications**

CPU: 4

Memory: 15 Gb

Storage: 60 Gb

Architecture: 64-bit

An m1.xlarge will fill these requirements quite well. However, you can probably get by with
an m1.large to save on costs.

.. note:: This installation requires external Internet connectivity.

**Recommended Images**

Start with a generic EC2 image from `Alestic <http://alestic.com/>`_ or the equivalent in
your environment. 

Install Chef Client - Ubuntu/Debian
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To install the chef client, use the following steps:


1. apt-get update && apt-get -y upgrade
2. curl -L http://www.opscode.com/chef/install.sh | sudo bash
3. apt-get -y install libmysqlclient-dev build-essential
4. /opt/chef/embedded/bin/gem install mysql

Here's a "one-liner"

.. code-block:: bash

   apt-get update && apt-get -y upgrade && curl -L http://www.opscode.com/chef/install.sh | sudo bash && apt-get -y install libmysqlclient-dev build-essential && /opt/chef/embedded/bin/gem install mysql


Be sure you're installing from home/ubuntu, or edit solo.rb accordingly.

Install Chef Client - Cent OS/Red Hat
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. warning:: Before you go further, disable selinux and reboot. Currently, the MySQL process will not
   start properly without disabling selinux. 

1. yum -y update && yum -y upgrade
2. curl -L http://www.opscode.com/chef/install.sh | sudo bash
3. yum -y install mysql-devel.x86_64
4. /opt/chef/embedded/bin/gem install mysql

Here's a "one-liner"

.. code-block:: bash

   yum -y update && yum -y upgrade && yum install curl && curl -L http://www.opscode.com/chef/install.sh | sudo bash && yum -y install mysql-devel.x86_64 && /opt/chef/embedded/bin/gem install mysql

Extract Chef-Solo Package
~~~~~~~~~~~~~~~~~~~~~~~~~

The installation chef-solo package will be provided to you by an enStratus engineer.
Extract it. You will see something like this.

.. code-block:: bash

   [root@localhost es-onpremise-chef-solo]# ls 
   classes  cookbooks  enstratus-utilities.jar README.md  roles  single_node.json  solo.rb

Download and Prepare the JDK and JCE
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

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

Key Generation
~~~~~~~~~~~~~~

As part of the installation process, you will have received a directory called `classes`
and a file called `enstratus-utilities.jar`.

.. note:: This command will only run on a system with java installed. Run this
   command from your local machine or any machine with with Java installed.

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

You could use the ones right here, but it's best to generate your own, since anyone with
these keys could potentially access your customer data.

You will use these values to fill in the attributes in the next step.

Edit Installation Attributes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can use the included single_node.json file or download one from here:

`single_node.json <http://es-download.s3.amazonaws.com/single_node.json>`_ 

Edit the file:

`single_node.json`

    Change console_url to what you want it to be. This will be the url you use to access the
    enStratus console. Example: cloud.mycompany.com

.. code-block:: bash

   "console_url" = ""

.. note:: In most cases, you'll have to make a hosts file entry for this url.

Change console_ip to an appropriate value.

.. code-block:: bash

   "console_ip" = ""

This value must be accessible to the console user. If you're installing in EC2, you most
probably want to use the publicly addressable IP address. 

.. note:: You'll need to open the firewall (security group) on port 443 to access the
   console.

Change source_cidr to the publicly addressable IP address of the installation host. If no
publicly routable IP address is available, use the primary IP address of the host.

.. code-block:: bash

   "source_cidr" = ""

The following values come from running:

.. code-block:: bash

   java -cp enstratus-utilities.jar:./classes/ net.enstratus.deploy.GenerateKeys

in the previous step.

.. code-block:: bash

   "dispatcherEncryptionKey" = ""
   "accessKey" = ""
   "encryptedManagementKey" = ""
   "firstEncryptedAccessKey" = ""
   "consoleEncryptionKey" = ""
   "secondEncryptedAccessKey" = ""


An enStratus engineer will provide this attribute along with the license key:

.. code-block:: bash

    "download":{"password":"REPLACE_ME"}

Example single_node.json
^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: json

   {
     "run_list": [ 
       "role[single_node]"
     ],
     "enstratus":{
       "console_url":"solo.enstratus.com",
       "license_key":"asdfasdfasdf123123123123",
       "console_ip":"204.236.184.191",
       "source_cidr":"204.236.184.191",
       "dispatcherEncryptionKey":"b%2MKnlmqVGIlGA6e%3T#QdYvxR&A0PeIC",
       "accessKey":"lk*zJgL&BJTAm$7j!TVb#AL6Hbhq5$",
       "encryptedManagementKey":"bd75e62e61c158f4df10a5d6448978d800067ab5dd1ade8d63528f53ea3b15e770ebb25331430114a1bb72663a6b03c5d55dc911c328d7f435270bcef52936f7",
       "firstEncryptedAccessKey":"3f7c501c59879aaa4631927bd164ffc64dc34b75bfe5f7f0a202f91533cc4495",
       "consoleEncryptionKey":"w!h!WTa^Qu85cwD&NE[xsv#&BuikwL6R2-N_bNSOpAIY(",
       "secondEncryptedAccessKey":"890e1013971b6fa826d37c2e910e79d014e620004931cabf4a09e3d73e8c09c6",
       "download":{"password":"asdfasdfasdfasdfa"},
       "database":{
                   "credentials_password":"somepassword",
                   "provisioning_password":"somepassword",
                   "analytics_password":"somepassword",
                   "console_password":"somepassword",
                   "enstratus_console_password":"somepassword"
                  },
       "km":{
             "xms":"512M",
             "xmx":"1024M",
             "init":"/services/km/bin",
             "port":"2013",
             "keystore":".keystore"
            },
       "dispatcher":{
                     "port":"3302",
                     "xms":"1024M",
                     "xmx":"2048M"
                    }
     },
     "MySQL":{
       "bind_address":"0.0.0.0"
     },
     "rabbitmq":{
       "version":"2.7.9"
     },
     "build_essential":{
       "compiletime":true
     }
   }

Install enStratus
~~~~~~~~~~~~~~~~~

Finally, it's time to install the enStratus software. As root:

.. code-block:: bash

   chef-solo -j single_node.json -c solo.rb
