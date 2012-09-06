.. _n_node_install:

N-node Installation
===================

So far we've demonstrated reference architectures for a 1- and 2-node enStratus cloud
management installation. We've leveraged a configuration management engine to make the
installation flexible by overriding installation parameters that changed due to the new
architecture and incorporated them into installation files that performed the necessary
installation actions.

Extending this concept a bit further, let's explore how to install enStratus on any number
of systems. To do this, we'll first map out the dependencies that exist between the
enStratus services and supporting software. The following sections outline what services
and configuration files are affected by changes to the architecture.

The way to use these sections is to read them like this: If I make a change to the KM
service/server, the services that are affected by these changes are dispatcher, monitor,
and worker, and the specific configuration files affected are...

KM
--

.. tabularcolumns:: |l|l|

+-------------+-----------------------------------------------------------------------------------------+
| Service     | Affected Files                                                                          |
+=============+=========================================================================================+
| Dispatcher  |  | ``/services/dispatcher/tomcat/webapps/ROOT/WEB-INF/classes/enstratus-km-client.cfg`` |
+-------------+-----------------------------------------------------------------------------------------+
| Monitor     |  | ``/services/monitor/classes/enstratus-km-client.cfg``                                |
+-------------+-----------------------------------------------------------------------------------------+
| Worker      |  | ``/services/worker/classes/enstratus-km-client.cfg``                                 |
+-------------+-----------------------------------------------------------------------------------------+
| MySQL       |  Grants                                                                                 |
+-------------+-----------------------------------------------------------------------------------------+

Override Values
~~~~~~~~~~~~~~~

To accommodate this type of change, you will need to add the following

.. code-block:: json

    "enstratus":{
                  "km_host":"ip_of_new_km_server"
                 }

Dispatcher
----------

.. tabularcolumns:: |l|l|

+----------------+-----------------------------------------------------------------------------------------+
| Service        | Affected Files                                                                          |
+================+=========================================================================================+
| API            |  | ``/services/api/tomcat/webapps/ROOT/WEB-INF/classes/enstratus-webservices.cfg``      |
+----------------+-----------------------------------------------------------------------------------------+
| Console        |  | ``/services/console/tomcat/webapps/ROOT/WEB-INF/classes/enstratus-webservices.cfg``  |
+----------------+-----------------------------------------------------------------------------------------+
| Console Worker |  | ``/services/cwrkr/classes/enstratus-webservices.cfg``                                |
+----------------+-----------------------------------------------------------------------------------------+
| Monitor        |  | ``/services/monitor/etc/cloud.properties``                                           |
|                |  | ``/services/monitor/classes/enstratus-provisioning.cfg``                             |
+----------------+-----------------------------------------------------------------------------------------+
| Worker         |  | ``/services/worker/classes/worker.properties``                                       |
|                |  | ``/services/worker/classes/enstratus-provisioning.cfg``                              |
+----------------+-----------------------------------------------------------------------------------------+
| MySQL          |  Grants                                                                                 |
+----------------+-----------------------------------------------------------------------------------------+

Override Values
~~~~~~~~~~~~~~~

To accommodate this type of change, you will need to add the following

.. code-block:: json

    "enstratus":{
                  "dispatcher_host":"ip_of_new_dispatcher_server"
                 }

MySQL
~~~~~

Be sure to add a grant statement to the provisioning and analytics databases to allow for
incoming connections from the newly changed dispatcher server.

Monitor
-------

If a change is made to the monitor server(s) such as the addition of a new node, or the
replacement of a node, the following services/files are affected:

.. tabularcolumns:: |l|l|

+----------------+---------------------------------------------------------------------------------------------+
| Service        | Affected Files                                                                              |
+================+=============================================================================================+
| Dispatcher     |  | ``/services/dispatcher/tomcat/webapps/ROOT/WEB-INF/classes/enstratus-provisioning.cfg``  |
+----------------+---------------------------------------------------------------------------------------------+
| Monitor        |  | ``/services/monitor/classes/enstratus-provisioning.cfg``                                 |
+----------------+---------------------------------------------------------------------------------------------+
| Worker         |  | ``/services/worker/classes/enstratus-provisioning.cfg``                                  |
+----------------+---------------------------------------------------------------------------------------------+

Override Values
~~~~~~~~~~~~~~~

To accommodate this type of change, you will need to add the following

.. code-block:: json

    "enstratus":{
                  "source_cidr":"ip_of_new_monitor_server,ip_of_another_monitor_server"
                 }

For source_cidr, use a comma-separated list of all the IP addresses of all the
worker, monitor, and dispatcher servers.

MySQL
~~~~~

Be sure to add a grant statement to the provisioning and analytics databases to allow for
incoming connections from the newly added/changed monitor server.


Worker
------

.. tabularcolumns:: |l|l|

+----------------+---------------------------------------------------------------------------------------------+
| Service        | Affected Files                                                                              |
+================+=============================================================================================+
| Dispatcher     |  | ``/services/dispatcher/tomcat/webapps/ROOT/WEB-INF/classes/enstratus-provisioning.cfg``  |
+----------------+---------------------------------------------------------------------------------------------+
| Monitor        |  | ``/services/monitor/classes/enstratus-provisioning.cfg``                                 |
+----------------+---------------------------------------------------------------------------------------------+
| Worker         |  | ``/services/worker/classes/enstratus-provisioning.cfg``                                  |
+----------------+---------------------------------------------------------------------------------------------+
| MySQL          |  Grants                                                                                     |
+----------------+---------------------------------------------------------------------------------------------+

Override Values
~~~~~~~~~~~~~~~

To accommodate this type of change, you will need to add the following

.. code-block:: json

    "enstratus":{
                  "source_cidr":"ip_of_new_worker_server,ip_of_another_worker_server"
                 }

For source_cidr, use a comma-separated list of all the IP addresses of all the
worker, monitor, and dispatcher servers.

MySQL
~~~~~

Be sure to add a grant statement to the provisioning and analytics databases to allow for
incoming connections from the newly added/changed worker server.

Console
-------

.. tabularcolumns:: |l|l|

+-------------+----------------+
| Service     | Affected Files |
+=============+================+
| MySQL       |  Grants        |
+-------------+----------------+

Override Values
~~~~~~~~~~~~~~~

None.

MySQL
~~~~~

Be sure to add a grant statement to the console and enstratus_console databases to allow
for incoming connections from the newly added/changed console server.

API
---

.. tabularcolumns:: |l|l|

+-------------+----------------+
| Service     | Affected Files |
+=============+================+
| MySQL       |  Grants        |
+-------------+----------------+

Override Values
~~~~~~~~~~~~~~~

None.

MySQL
~~~~~

Be sure to add a grant statement to the console and enstratus_console databases to allow
for incoming connections from the newly added/changed API server.

MQ
--

.. tabularcolumns:: |l|l|

+------------+------------------------------------------------------------------------+
| Service    | Affected Files                                                         |
+============+========================================================================+
| Dispatcher |  | ``/services/dispatcher/tomcat/webapps/ROOT/WEB-INF/classes/mq.cfg`` |
+------------+------------------------------------------------------------------------+
| Monitor    |  | ``/services/monitor/etc/cloud.properties``                          |
|            |  | ``/services/monitor/classes/mq.cfg``                                |
+------------+------------------------------------------------------------------------+
| Worker     |  | ``/services/worker/classes/worker.properties``                      |
|            |  | ``/services/worker/classes/mq.cfg``                                 |
+------------+------------------------------------------------------------------------+

Override Values
~~~~~~~~~~~~~~~

To accommodate this type of change, you will need to add the following

.. code-block:: json

    "enstratus":{
                  "mq_host":"ip_of_new_mq_server"
                 }

MySQL
-----

.. tabularcolumns:: |l|l|

+------------+----------------------------------------------------------------------+
| Service    | Affected Files                                                       |
+============+======================================================================+
| API        |  | ``/services/api/tomcat/webapps/ROOT/META-INF/context.xml``        |
+------------+----------------------------------------------------------------------+
| Console    |  | ``/services/console/tomcat/webapps/ROOT/META-INF/context.xml``    |
+------------+----------------------------------------------------------------------+
| KM         |  | ``/services/km/tomcat/webapps/ROOT/META-INF/context.xml``         |
+------------+----------------------------------------------------------------------+
| Dispatcher |  | ``/services/dispatcher/tomcat/webapps/ROOT/META-INF/context.xml`` |
+------------+----------------------------------------------------------------------+
| Monitor    |  | ``/services/monitor/etc/cloud.properties``                        |
+------------+----------------------------------------------------------------------+
| Worker     |  | ``/services/worker/classes/worker.properties``                    |
+------------+----------------------------------------------------------------------+

Override Values
~~~~~~~~~~~~~~~

To accommodate this type of change, you will need to add the following

.. code-block:: json

    "enstratus":{
                  "mysql_hostname":"ip_or_hostname_of_mysql_server"
                 }

Riak
----

The following services/files depend on Riak:

.. tabularcolumns:: |l|l|

+------------+-----------------------------------------------------------------------------------------------+
| Service    | Affected Files                                                                                |
+============+===============================================================================================+
| API        |  | ``/services/api/tomcat/webapps/ROOT/WEB-INF/classes/dasein-persistence.properties``        |
+------------+-----------------------------------------------------------------------------------------------+
| Console    |  | ``/services/console/tomcat/webapps/ROOT/WEB-INF/classes/dasein-persistence.properties``    |
+------------+-----------------------------------------------------------------------------------------------+
| KM         |  | ``/services/km/tomcat/webapps/ROOT/WEB-INF/classes/dasein-persistence.properties``         |
+------------+-----------------------------------------------------------------------------------------------+
| Dispatcher |  | ``/services/dispatcher/tomcat/webapps/ROOT/WEB-INF/classes/dasein-persistence.properties`` |
+------------+-----------------------------------------------------------------------------------------------+
| Monitor    |  | ``/services/monitor/etc/dasein-persistence.properties``                                    |
+------------+-----------------------------------------------------------------------------------------------+
| Worker     |  | ``/services/worker/classes/dasein-persistence.properties``                                 |
+------------+-----------------------------------------------------------------------------------------------+

Override Values
~~~~~~~~~~~~~~~

To accommodate this type of change, you will need to add the following

.. code-block:: json

    "enstratus":{
                  "riak_host":"ip_or_hostname_of_riak_server",
                  "riak_port":"port_upon_which_riak_runs"
                 }

JDK
---

The following services/files depend on the JDK:

.. tabularcolumns:: |l|l|

+------------+-----------------------------------------+
| Service    | Affected Files                          |
+============+=========================================+
| API        |  | ``/services/api/bin/tomcat``         |
+------------+-----------------------------------------+
| Console    |  | ``/services/console/bin/tomcat``     |
+------------+-----------------------------------------+
| KM         |  | ``/services/km/bin/tomcat``          |
+------------+-----------------------------------------+
| Dispatcher |  | ``/services/dispatcher/bin/tomcat``  |
+------------+-----------------------------------------+
| Monitor    |  | ``/services/monitor/bin/assign``     |
|            |  | ``/services/monitor/bin/controller`` |
|            |  | ``/services/monitor/bin/poll``       |
+------------+-----------------------------------------+
| Worker     |  | ``/services/worker/bin/publisher``   |
|            |  | ``/services/worker/bin/subscriber``  |
+------------+-----------------------------------------+

Override Values
~~~~~~~~~~~~~~~

To accommodate this type of change, you will need to add the following

.. code-block:: json

    "enstratus":{
                  "java_home":"/path/to/java"
                }

Keep in mind when changing the JDK, that enStratus only works with version 6 and is
dependent upon the Java Cryptographic Extensions.
