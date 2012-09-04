Key Manager
===========

The enStratus Key/Credentials Management service is a tomcat service installed to
/services/km. 

The enStratus KM service is very stable and will run for very long periods of time
without requiring attention.

.. figure:: ./images/km.png
   :height: 250 px
   :width: 450 px
   :scale: 70 %
   :alt: KM Service
   :align: center

   KM Service Connections

Overview
--------

The enStratus KM service is a tomcat process that listens on port 2013, configurable. It
accepts connections from enStratus components that have as an operational requirement to
perform activities that require access to sensitive information.

Sensitive information can be thought of as any information required to interact with
cloud providers, for example, the credentials necessary to perform the action of
initiating the start of a VM in a cloud provider.

The KM system also handles information entered by users via the enStratus console or API.
An example of this type of information is a public ssh key that may be a part of an
enStratus user profile or an SSL certificate that was loaded into enStratus for use in an
automation routine.

The KM system stores this information in an encrypted, de-identified database called the
credentials database. 

Credentials Database
--------------------

The credentials database stores the encrypted, de-identified information described above.
The credentials database is currently a MySQL database, but will be migrated to Riak in a
future release.

The enStratus KM system is the **only** enStratus component to interact with the
credentials database.

The credentials database should be considered to be the only enStratus component that,
should it be compromised or destroyed, would constitute an irrecoverable failure. As such,
care should be taken to ensure the data contained therein is backed up and safeguarded in
accordance with industry standard procedures.

Installation
------------

Installation of the enStratus KM service is best handled by using a configuration
management system such as Chef or Puppet.

Software Requirements
---------------------

The KM service depends on the Java 6 Java Development Kit (JDK) provided by Oracle, along with the associated Java
Cryptographic Extensions (JCE).

The JDK is installed to /usr/local/jdk and the jce jars are installed to /usr/local/jdk/security/lib.

Incoming Connections
--------------------

#. Dispatcher

   The enStratus dispatcher component connects to the KM service via a webservices
   endpoint, defined on the dispatcher service in:
  
   ``/services/dispatcher/tomcat/webapps/ROOT/WEB-INF/classes/enstratus-km-client.cfg``

#. Monitor

   The enStratus monitor component connects to the KM service via a webservices
   endpoint, defined on the monitor service in:

   ``/services/monitor/classes/enstratus-km-client.cfg``

#. Worker

   The enStratus worker component connects to the KM service via a webservices
   endpoint, defined on the worker service in:

   ``/services/worker/classes/enstratus-km-client.cfg``

Outgoing Connections
--------------------

None.

Customizing
-----------

The service port upon which the enStratus KM service listens and the Java options it uses
to start the tomcat service can be modified.

Service Port
~~~~~~~~~~~~

The service port upon which the KM service operates is defined in:

``/services/km/tomcat/conf/server.xml``

This file also defines the Java Keystore location for loading certificates to the KM
tomcat service.

JAVA_OPTS
~~~~~~~~~

Modifying JAVA_OPTS can be done through the file:

``/services/km/bin/tomcat``

Logging
-------

Logging for the enStratus KM service is done to /services/km/tomcat/logs/catalina.out and
is controlled by /services/km/tomcat/webapps/ROOT/WEB-INF/classes/log4j.{xml,properties}

Monitoring
----------

Monitoring the enStratus KM service can be done using traditional monitoring tools such as
monit or nagios. 

JMX
~~~

Backups
-------

Service
~~~~~~~

The enStratus KM service files should be backed up before and after any changes, and
once/day during steady-state operations.

Database
~~~~~~~~

The frequency with which the enStratus credentials database is backed up is determined
primarily by the number of writes being made to the database. enStratus environments where
there are many new accounts being joined to enStratus, many new users being added or
modified should conduct backups more frequently than environments where these events are
less frequent.

As a general best practice guideline, backups should be done no less frequent than twice
daily, every four hours in heavily utilized systems, or more frequently as the situation
dictates.

Backups should be encrypted and stored in a geographically unique location from the
primary data source.

The expected time to run a backup of the credentials database is less than one minute. 

The expected time to restore the credentials database from backup less than one minute.

Starting KM
-----------

To start the Key Management service:

.. code-block:: bash

	/etc/init.d/enstratus-km start

KM Start Process
~~~~~~~~~~~~~~~~

The init script passes the start argument to /services/km/bin/tomcat, which starts the km service.

.. code-block:: bash

	Starting Key Manager.
	Using CATALINA_BASE:   /services/km/tomcat
	Using CATALINA_HOME:   /services/km/tomcat
	Using CATALINA_TMPDIR: /services/km/tomcat/temp
	Using JRE_HOME:       /usr/lib/jvm/java-6-sun

The tomcat service will start, and you should see a java service running on port 2013.

.. code-block:: bash

	netstat -tnlup | grep 2013
	tcp6       0      0 :::2013                 :::*                    LISTEN 7159/java  

Stopping KM
-----------

To stop the Key Management service:

.. code-block:: bash

	/etc/init.d/enstratus-km stop

	Stopping Key Manager.
	Using CATALINA_BASE:   /services/km/tomcat
	Using CATALINA_HOME:   /services/km/tomcat
	Using CATALINA_TMPDIR: /services/km/tomcat/temp
	Using JRE_HOME:       /usr/lib/jvm/java-6-sun

KM Stop Process
~~~~~~~~~~~~~~~
The init script passes the stop argument to /services/km/bin/tomcat, which stops the km service.


Configuration Files
-------------------

The KM service has two configuration files:

#. context.xml
#. server.xml

context.xml
~~~~~~~~~~~

The full path to the context xml configuration file is:

``/services/km/tomcat/webapps/ROOT/META-INF/context.xml``

This file is responsible for controlling how the KM service connects to the credentials
database.

server.xml
~~~~~~~~~~

The full path to the server.xml configuration file is:

``/services/km/tomcat/conf/server.xml``

The server.xml is responsible for controlling the start of the KM service itself. This is
the place to change the listening and shutdown port of the KM service.
