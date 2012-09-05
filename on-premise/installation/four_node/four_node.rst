.. _four_node_install:

Four Node
=========

Taking the information provided in the n-node document, we can now begin to describe
almost any enStratus architecture and craft configuration files to support a configuration
management engine automated installation.

To illustrate, let's construct a four-node installation of enStratus as shown in the
diagram below.

.. figure:: ./images/four_node.png
   :height: 400px
   :width: 600 px
   :scale: 75 %
   :alt: enStratus Four Node Architecture
   :align: center

   enStratus Four Node Architecture


#. Database Node

   The database node will only run the database services required by enStratus, MySQL and
   Riak.

#. Console Node

   The console node will run the "frontend" services that were installed on the two_node
   frontend server, this time leaving off MySQL, which is now running on the dedicated
   database node.
   
#. Dispatcher Node

   The dispatcher node will run the dispatcher, KM, and Rabbit MQ services.
   
#. Worker Node

   The worker node will only run the monitor and worker processes.
   
Configuration Files
-------------------

For those who have gone through the two-node installation process, the approach here will
be similar. We'll construct this time 4 json files to control the installation of the
components on each node and override the parameters needed to properly write the
configuration files.

We'll call these files:

#. four_node_database.json
   
   Download: `four_node_database.json <http://es-download.s3.amazonaws.com/four_node_database.json>`_ 
   
#. four_node_console.json

   Download: `four_node_console.json <http://es-download.s3.amazonaws.com/four_node_console.json>`_ 

#. four_node_dispatcher.json

   Download: `four_node_dispatcher.json <http://es-download.s3.amazonaws.com/four_node_dispatcher.json>`_ 

#. four_node_worker.json

   Download: `four_node_worker.json <http://es-download.s3.amazonaws.com/four_node_worker.json>`_ 

Dependencies
------------

four_node_database
~~~~~~~~~~~~~~~~~~

The database node has no external dependencies, except that it needs to grant privileges
to the services running on the other servers. Here is a table that summarizes the services
and their dependent databases.

+-------------------+------------------------------+
| Database          | Service(s)                   |
+===================+==============================+
| credentials       | KM                           |
+-------------------+------------------------------+
| provisioning      | Dispatcher, Worker, Monitor  |
+-------------------+------------------------------+
| analytics         | Dispatcher, Worker, Monitor  |
+-------------------+------------------------------+
| console           | Console, API, Console Worker |
+-------------------+------------------------------+
| enstratus_console | Console, API, Console Worker |
+-------------------+------------------------------+

.. note:: Automated granting of privileges in MySQL has not been completed yet, sorry.
          
          Coming soon!

**Database Role**

The role applied to the database node will be the four_node_database role, defined in
roles/four_node_database.json.

.. code-block:: json

   {
     "name": "four_node_database",
     "default_attributes": { },
     "override_attributes": { },
     "json_class": "Chef::Role",
     "description": "Four node role for the database node.",
     "chef_type": "role",
     "run_list": 
      [ "recipe[enstratus::default]",
         "recipe[enstratus::limits]",
         "role[MySQL]",
         "role[riak]",
         "role[backendDB]",
      ]   
   }


four_node_console
~~~~~~~~~~~~~~~~~

The console server needs to know how to reach MySQL, Riak, and the dispatcher web service,
so we need to override the attributes to allow for this. In the four_node_console.json
file, these values are found here:

.. code-block:: json

    "riak_host":"DATABASE_IP",
    "mysql_hostname":"DATABASE_IP",
    "dispatcher_hostname":"DISPATCHER_IP",

**Console Role**

The other critical item to change is the role applied to this node, in this case the role
called four_node_console is specified, which directs the installation of the console, API,
and console-worker (cwrkr) services.

The role applied to the console node will be the four_node_console role, defined in
roles/four_node_console.json.

.. code-block:: json

   {
     "name": "four_node_console",
     "default_attributes": { },
     "override_attributes": { },
     "json_class": "Chef::Role",
     "description": "Four node role for the console node.",
     "chef_type": "role",
     "run_list": 
       [   
         "recipe[enstratus::default]",
         "recipe[enstratus::limits]",
         "role[sudo]",
         "role[console]",
         "role[api]",
         "role[cwrkr]"
       ]   
   }

four_node_dispatcher
~~~~~~~~~~~~~~~~~~~~

The dispatcher server needs to know how to reach MySQL, Riak, and the KM service. The KM
service in this case is actually running on localhost from the perspective of the
dispatcher service, so we don't need to override it. In the four_node_dispatcher.json, we
override:

.. code-block:: json

    "riak_host":"DATABASE_IP",
    "mysql_hostname":"DATABASE_IP"


**Dispatcher Role**

The role applied to the dispatcher node will be the four_node_dispatcher role, defined in
roles/four_node_dispatcher.json.

.. code-block:: json

   {
     "name": "four_node_dispatcher",
     "default_attributes": { },
     "override_attributes": { },
     "json_class": "Chef::Role",
     "description": "Four node role for the dispatcher node.",
     "chef_type": "role",
     "run_list": 
      [ "recipe[enstratus::default]",
         "recipe[enstratus::limits]",
         "role[sudo]",
         "role[mq]",
         "role[km]",
         "role[dispatcher]",
      ]   
   }


four_node_worker
~~~~~~~~~~~~~~~~

The worker server is running both the monitor and worker services, and needs to know how
to reach the KM and Rabbit MQ services running on the dispatcher node, and also MySQL and
Riak running on the database node. In the four_node_worker.json, we override:

.. code-block:: json

    "riak_host":"DATABASE_IP",
    "mysql_hostname":"DATABASE_IP"
    "dispatcher_hostname":"DISPATCHER_IP",
    "km_hostname":"DISPATCHER_IP",

**Worker Role**

The role applied to the worker will be the four_node_worker role, defined in
roles/four_node_worker.json.

.. code-block:: json

   {
     "name": "four_node_worker",
     "default_attributes": { },
     "override_attributes": { },
     "json_class": "Chef::Role",
     "description": "Four node role for the worker node.",
     "chef_type": "role",
     "run_list":
      [ "recipe[enstratus::default]",
         "recipe[enstratus::limits]",
         "role[sudo]",
         "role[monitor]",
         "role[worker]"
      ]
   }

