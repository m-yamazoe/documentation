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

   The monitor node will only run the monitor and worker processes.
   
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

.. note:: Automated granting of privileges has not been completed yet, sorry. Coming soon!

four_node_console
~~~~~~~~~~~~~~~~~

The console server needs to know how to reach MySQL, Riak, and the dispatcher webservice,
so we need to override the attributes to allow for this. In the four_node_console.json
file, these values are found here:

.. code-block:: json

    "riak_host":"DATABASE_IP",
    "dispatcher_hostname":"DATABASE_IP",
    "mysql_hostname":"DATABASE_IP",

The other critical item to change is the role applied to this node, in this case the role
called four_node_console is specified, which directs the installation of the console, API,
and console-worker (cwrkr) services.

four_node_dispatcher
~~~~~~~~~~~~~~~~~~~~

The dispatcher server needs to know how to reach MySQL, Riak, and the KM service. The KM
service in this case is actually running on localhost from the perspective of the
dispatcher service, so we don't need to override it. In the four_node_dispatcher.json, we
override:

    "riak_host":"BACKEND_IP",
    "mysql_hostname":"BACKEND_IP"

