.. _two_node_install:

+------------------------+---------------------------+----------+
| Service                | Service Dependencies      | Header 4 |
+========================+===========================+==========+
| API                    | Riak                      | column 4 |
|                        | MySQL, console            | column 4 |
|                        | MySQL, enstratus_console  | column 4 |
+------------------------+------------+--------------+----------+
| body row 2             | Cells may span columns.              |
+------------------------+------------+-------------------------+
| body row 3             | Cells may  | - Table cells           |
+------------------------+ span rows. | - contain               |
| body row 4             |            | - body elements.        |
+------------------------+------------+-------------------------+

+---------+-----------------------+
| Service | Affected Files        |
+=========+=======================+
| Worker  |  | File 1             |
|         |  | File 2             |
+---------+-----------------------+
| Monitor |  | File 1             |
|         |  | File 2             |
+---------+-----------------------+

Two Node
--------

.. figure:: ./images/two_node.png
   :height: 400px
   :width: 600 px
   :scale: 75 %
   :alt: enStratus Two Node Architecture
   :align: center

   enStratus Two Node Architecture

The two-node enStratus deployment separates the software into so-called back end and
front end components.

**Back End Services**

1. Dispatcher

  * Provisioning Database (MySQL)
  * Analytics Database (MySQL)

2. Workers
3. Monitors
4. Key Manager

  * Credentials Database (MySQL)

5. Riak
6. Rabbit MQ

**Front End Services**

1. Console

  * Console Database (MySQL)
  * enStratus Console Database (MySQL)

2. API
3. Console Worker

Overview
~~~~~~~~

A chef-solo cookbook is provided to facilitate the installation of software prerequisites
along with installing the enStratus software.

The two-node enStratus installation leverages chef roles to separate the installation into
the front and back end components.

System Requirements
~~~~~~~~~~~~~~~~~~~

Provision two servers (can be virtual, we often test using m1.large instances in EC2) for
the installation.

Recommended  Specifications (Backend)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

CPU: 4

Memory: 12 Gb

Storage: 60 Gb

Architecture: 64-bit

Again, an m1.xlarge will fill these requirements quite well. You can probably get by with
an m1.large to save on costs.

Recommended Specifications (Frontend)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

CPU: 2

Memory: 8 Gb

Storage: 30 Gb

Architecture: 64-bit

An m1.large is sufficient to meet the frontend requirements.

Recommended Images
^^^^^^^^^^^^^^^^^^

Start with a generic EC2 image from `Alestic <http://alestic.com/>`_ or the equivalent in
your environment. 

Installation Procedure
~~~~~~~~~~~~~~~~~~~~~~

The installation procedure will move through the following steps:

#. Install Chef client (both)

#. Extract chef-solo package (both)

#. Download and prepare the JDK and JCE (both)

#. Generate keys (once)

#. Edit installation attributes

#. Install enStratus (backend)

#. Install enStratus (frontend)

Installation Steps
~~~~~~~~~~~~~~~~~~

Install Chef client
^^^^^^^^^^^^^^^^^^^

Follow the instructions given in the single node installation instructions for installing
the chef client on both of the nodes.

Download and Prepare the JDK and JCE
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Download and prepare the JDK and JCE on each of the nodes according to the instructions
given in the single node installation.

Key Generation
^^^^^^^^^^^^^^

Generate keys on a system where Java is installed according to the instructions provided
in the single node installer.

.. warning:: Generate only **one** set of credentials and use it for both the frontend and
   backend server.

Edit Installation Attributes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

For the installation attributes, we're going to **override** the settings used to install
on a single node to configure enStratus for installation on two nodes. This installation
assumes you are going to install enStratus according to the architecture described in the
beginning of this document. 

From this point forward, the installation process will proceed in a way that should be
familiar to those who have installed on a single node. The difference between the single
node and the two node installation is that there will be two separate chef runs (hopefully
that part wasn't a surprise) and two files that control them:

#. frontend.json
#. backend.json

Edit each of these files to replace the appropriate variables to accommodate the
connections that need to be made from the frontend services (console, API) to the backend
dispatcher service.

**frontend.json**

.. code-block:: json
   :emphasize-lines: 3,17-18

   {
     "run_list": [ 
       "role[frontend]"
     ],  
     "enstratus":{
       "console_url":"CHANGE_ME",
       "license_key":"CHANGE_ME",
       "console_ip":"CHANGE_ME",
       "source_cidr":"CHANGE_ME",
       "dispatcherEncryptionKey":"CHANGE_ME",
       "accessKey":"CHANGE_ME",
       "encryptedManagementKey":"CHANGE_ME",
       "firstEncryptedAccessKey":"CHANGE_ME",
       "consoleEncryptionKey":"CHANGE_ME",
       "secondEncryptedAccessKey":"CHANGE_ME",
       "download":{"password":"CHANGE_ME"},
       "riak_host":"BACKEND_IP",
       "dispatcher_hostname":"BACKEND_IP",
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

The highlighted lines indicate the changes from the single_node.json file. The enStratus
console and API services must be able to reach the Riak service and the dispatcher service
running on the backend server. Replace BACKEND_IP with the IP address of the server that
will run the backend services. Hostname is also acceptable, provided the hosts know how to
resolve them.

The only other change was a modification to the run list, in this case, the expected run
list is only those components listed near the beginning of this guide for the frontend
server. 

The application of the frontend run list is described by the frontend role, located in
``roles/frontend.json``.

**backend.json**

.. code-block:: json
   :emphasize-lines: 3

   {
     "run_list": [ 
       "role[backend]"
     ],
     "enstratus":{
       "console_url":"CHANGE_ME",
       "license_key":"CHANGE_ME",
       "console_ip":"CHANGE_ME",
       "source_cidr":"CHANGE_ME",
       "dispatcherEncryptionKey":"CHANGE_ME",
       "accessKey":"CHANGE_ME",
       "encryptedManagementKey":"CHANGE_ME",
       "firstEncryptedAccessKey":"CHANGE_ME",
       "consoleEncryptionKey":"CHANGE_ME",
       "secondEncryptedAccessKey":"CHANGE_ME",
       "download":{"password":"CHANGE_ME"},
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

Since the backend server in this architectural configuration makes no outbound connections
to any of the services installed on the console server, the only necessary change is to
alter the run list to direct the installation of the backend services, highlighted in line
3.

Install enStratus
^^^^^^^^^^^^^^^^^

Finally, it's time to install the enStratus software. As root:

1. Frontend Server

.. code-block:: bash

   chef-solo -j frontend.json -c solo.rb

2. Backend Server

.. code-block:: bash

   chef-solo -j backend.json -c solo.rb
