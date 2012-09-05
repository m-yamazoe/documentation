.. _dispatcher:

Dispatcher
==========

The enStratus Dispatcher service is a tomcat service installed to /services/dispatcher.

.. figure:: ./images/dispatcher.png
   :height: 300 px
   :width: 550 px
   :scale: 70 %
   :alt: Dispatcher Service
   :align: center

   Dispatcher Service Connections

Overview
--------

The enStratus dispatcher service is a tomcat process that listens on port 3302,
configurable. It accepts connections from enStratus components that have as an operational
requirement to interact with cloud providers, and from guest virtual machines that have
the enStratus agent installed.

The dispatcher service is in a sense the heart of the enStratus cloud management system.
It is responsible for carrying out the actions initiated by end users via the enStratus
console or via the API.

When actions are taken by users, an enStratus job is created, and a message is pushed onto
the message queue for action by the enStratus monitor and worker systems.

The completion of a job is determined by the result discovered by the enStratus monitor or
workers systems and depends on the type of job initiated.

When actions that require accessing cloud or other credentials stored in the credentials
database are initiated by users or triggered in an automated way, the dispatcher
service contacts the enStratus Key Management (KM) service, which in turn access the
information contained in the credentials database.

Communication to and from the dispatcher service to the KM service is encrypted.

The dispatcher service interacts with two MySQL databases: provisioning and analytics.

Provisioning Database
---------------------

The provisioning database stores cloud-related information. As enStratus interacts with
cloud providers, information discovered about the clouds capabilities, user subscriptions,
accounts, permissions, and infrastructure are stored in this database.

Some examples of the type of information stored in this database are:

#. Cloud "subscriptions".
 
   A cloud "subscription" is a mechanism enStratus uses to track the capabilities provided
   by the cloud account being integrated into the enStratus cloud management system. It is
   possible, for example, to connect an EC2 account to enStratus that has not yet signed up
   for the Amazon S3 service. enStratus will not present the Platform > Files option to the
   end user in this case.

   Should the administrator of the Amazon account later subscribe to the S3 service, no
   changes are required in enStratus. enStratus will automatically detect this change and
   present the Platform > Files option in the console. Please note that changes like this may
   take a few hours to be discovered.

#. Accounts.

   enStratus tracks cloud accounts in the provisioning database. Each cloud account 
   connected to enStratus will carry with it unique information such as the cloud regions 
   visible for the credentials entered and the default budget code assigned to
   infrastructure discovered.

#. Users, groups, roles.

   enStratus stores information related to users that access the enStratus cloud
   management system such as the user id, any groups of which the enStratus user is a part,
   and any budget codes to which the user has access.

   The provisioning database is also where the mapping of an enStratus group to an
   enStratus role is made

#. Budget Codes and their state.

   The provisioning database contains information about the costs being incurred as part
   of operating in cloud environments. For example, it is possible to assign a unique cost
   identifier to a cloud product such as a server and have the operational costs tracked
   using the enStratus budget feature.

#. Clouds

   The provisioning database contains information that defines the connection point to
   cloud providers. 

There is no inherently sensitive information stored in the provisioning database. Account
information discussed above does not include credentials. User information such as email
addresses used to access enStratus are stored in the provisioning database.

The provisioning database must allow access from the dispatcher service, and all monitor
and worker servers.

The provisioning database should be considered to be critical to the operation of the
enStratus cloud management system. 

Analytics Database
------------------

The enStratus analytics database contains information gathered by enStratus to perform
analysis of server operations and other metrics.

The analytics database must allow access from the dispatcher service, and all monitor
and worker servers.

The analytics database contains no sensitive information and should not be considered to
be critical to the operation of the enStratus cloud management system.

Installation
------------

Installation of the enStratus dispatcher service is best handled by using a configuration
management system such as Chef or Puppet.

Software Requirements
---------------------

The dispatcher service depends on the Java 6 Java Development Kit (JDK) provided by Oracle, along with the associated Java
Cryptographic Extensions (JCE).

The JDK is installed to /usr/local/jdk and the jce jars are installed to /usr/local/jdk/security/lib.

Incoming Connections
--------------------

The dispatcher service is primarily responsible for carrying out the actions initiated by
users via the console or the enStratus API.

#. Console

   The enStratus console system connects to the dispatcher service via a webservices
   endpoint, defined on the console service in:
  
   /services/console/tomcat/webapps/ROOT/WEB-INF/classes/enstratus-webservices.cfg

#. API

   The enStratus API system connects to the dispatcher service via a webservices
   endpoint, defined on the api service in:
  
   /services/api/tomcat/webapps/ROOT/WEB-INF/classes/enstratus-webservices.cfg

#. Guest VM running in a cloud that have the enStratus agent installed will attempt to
   connect to the enStratus dispatcher service on port 3302. This connection is defined on
   the guest VM in:

   /enstratus/ws/tomcat/webapps/ROOT/WEB-INF/classes/enstratus-webservices.cfg

   Although the communication is bi-directional, the only time a guest VM will initiate a
   connection to the enStratus dispatcher service is upon agent start. The remainder of the
   communications are from the dispatcher to the agent.

Outgoing Connections
--------------------

#. Cloud API
   
   The dispatcher service initiates communication to the cloud provider's API to take
   actions on behalf of users utilizing the enStratus console or API.

#. Guest VM

   The dispatcher service will initiates connections to the enStratus agent running on
   guest VM in the cloud as necessary to trigger automated and user-initiated actions.

#. KM
   
   The dispatcher requires a connection to the enStratus KM system to perform actions that
   require credentials. A webservices call is made to the enStratus KM service to retrieve
   the credentials. This communication is encrypted using industry standard AES-256
   encryption.

#. Riak

   The dispatcher service connects to the Riak database to store and retrieve persistent
   information about cloud resources, among other things.

#. MySQL
 
   The dispatcher service connects to the MySQL database to store and retrieve persistent
   information about cloud resources, among other things.
   
Customizing
-----------

The service port upon which the enStratus dispatcher service listens and the Java options it uses
to start the tomcat service can be modified.

Service Port
~~~~~~~~~~~~

The service port upon which the KM service operates is defined in:

/services/dispatcher/tomcat/conf/server.xml

This file also defines the Java Keystore location for loading certificates to the
dispatcher tomcat service.

JAVA_OPTS
~~~~~~~~~

Modifying JAVA_OPTS can be done through the file:

/services/dispatcher/bin/tomcat

Logging
-------

Logging for the enStratus dispatcher service is done to
/services/dispatcher/tomcat/logs/catalina.out and is controlled by
/services/dispatcher/tomcat/webapps/ROOT/WEB-INF/classes/log4j.{xml,properties}

The contents of the dispatcher logs depends on the logging level being used by the
dispatcher process. The logging level is set in:

.. code-block:: bash

   /services/dispatcher/tomcat/webapps/ROOT/WEB-INF/classes/log4j.xml

#. INFO

   With INFO logging set, very little information will be passed to the log file. 

#. WARN

#. DEBUG

#. TRACE

   Setting the TRACE logging level will produce very verbose output that can be useful in
   determining where a problem may exist. For example, if a cloud provider is out of capacity
   or API calls are failing, TRACE logging will allow an administrator to quickly determine
   the source of an issue.

Monitoring
----------

Monitoring the enStratus dispatcher service can be done using traditional monitoring tools such as
monit or nagios. 

JMX
~~~

JMX monitoring notes.

Backups
-------

Service
~~~~~~~

The enStratus dispatcher service files should be backed up before and after any changes, and
once/day during steady-state operations. An example of a backup is shown here, excluding
the log files in this case.

.. code-block:: bash

   cd /services/dispatcher/
   tar -czf /dispatcherService.tar.gz --exclude='tomcat/logs/*' . > /dev/null 2>&1


Databases
~~~~~~~~~

The frequency with which the enStratus provisioning database is backed up is determined
primarily by the number of writes being made to the database. enStratus environments where
there are many new accounts being joined to enStratus, many new users being added or
modified should conduct backups more frequently than environments where these events are
less frequent.

As a general best practice guideline, backups should be done no less frequent than twice
daily, every four hours in heavily utilized systems, or more frequently as the situation
dictates.

Backups should be encrypted and stored in a geographically unique location from the
primary data source.

The expected time to run a backup of the provisioning database can vary greatly. In
enStratus deployments that have been running for a very long period of time, the backup
may take between 10 and 40 minutes.

The expected time to restore the provisioning database can vary depending on the length of
time of the existence of the provisioning database, the amount of hardware backing the db,
and the amount of data contained. Restoration may take over an hour, but probably less.

The same principles apply for the analytics database, although it typically has less
information in it than the provisioning database.

Starting Dispatcher
-------------------

To start the Dispatcher service:

.. code-block:: bash

	/etc/init.d/enstratus-dispatcher start

Dispatcher Start Process
~~~~~~~~~~~~~~~~~~~~~~~~

The dispatcher init script performs two actions:

#. Executes /services/dispatcher/bin/pinger. This starts the pinger service.

#. Passes the start argument to /services/dispatcher/bin/tomcat, which starts the dispatcher service. 

.. code-block:: bash

	Starting pinger.
	Starting Dispatcher.
	Using CATALINA_BASE:   /services/dispatcher/tomcat
	Using CATALINA_HOME:   /services/dispatcher/tomcat
	Using CATALINA_TMPDIR: /services/dispatcher/tomcat/temp
	Using JRE_HOME:       /usr/lib/jvm/java-6-sun

The pinger is an auxiliary service which is responsible for performing a type of heartbeat operation for
virtual machines provisioned in the cloud.

The dispatcher service will start, and a java service will run on port 3302.

.. code-block:: bash

	netstat -tnlup | grep 3302
	tcp6       0      0 :::3302                 :::*                    LISTEN 7199/java  

Stopping Dispatcher
-------------------

To stop the Dispatcher service:

.. code-block:: bash

	/etc/init.d/enstratus-dispatcher stop

	root@ubuntu:/home/greg# stopDispatcher 
	Stopping Dispatcher.
	Using CATALINA_BASE:   /services/dispatcher/tomcat
	Using CATALINA_HOME:   /services/dispatcher/tomcat
	Using CATALINA_TMPDIR: /services/dispatcher/tomcat/temp
	Using JRE_HOME:       /usr/lib/jvm/java-6-sun

Dispatcher Stop Process
~~~~~~~~~~~~~~~~~~~~~~~

The dispatcher init script passes the stop argument to /services/dispatcher/bin/tomcat, which stops the dispatcher
service.

.. note:: The stop argument does not stop the pinger service. This is expected behavior.


Dispatcher Troubleshooting
--------------------------

The dispatcher is a very stable process and does not require much attention. However,
here are some areas to consider when managing the dispatcher process.

1. Restarting the Dispatcher

.. note:: Stopping the dispatcher service will cause enStratus to be unusable. 

Here are some helpful commands to stop and start the dispatcher service, as well as
tail the logs. Put these commands in your .bashrc as an alias or a function.

  1. alias startDispatcher='/etc/init.d/enstratus-dispatcher start'

  2. alias stopDispatcher='/etc/init.d/enstratus-dispatcher stop'

  3. alias tailDispatcher='tail -f /services/dispatcher/tomcat/logs/catalina.out'

Once these are set, start the dispatcher process like this:

.. code-block:: bash

  startDispatcher && tailDispatcher

And you'll be able to start and tail the logs in one line. Very helpful. Why is tailing
the log useful?

2. Registering for the first time

3. Entering Cloud Credentials

   It can be helpful to watch the dispatcher logs when entering cloud credentials.

4. Log sizes 

   If the installation is new, it is quite likely that the logging levels are set high

Configuration Files
-------------------

The dispatcher service has 10 configuration files:

.. hlist::
   :columns: 2

   * tomcat
   * enstratus
   * pinger
   * context.xml
   * mq.cfg
   * enstratus-km-client.cfg
   * enstratus-provisioning.cfg
   * dasein-persistence.properties
   * server.xml
   * enstratus-dispatcher

tomcat
~~~~~~

Path:

  ``/services/dispatcher/bin/tomcat``

This file is responsible for controlling the start of the dispatcher service. Any
JAVA_OPTS that need to be passed to the dispatcher tomcat service can be done using this
file.

enstratus
~~~~~~~~~

Path:

  ``/services/dispatcher/bin/enstratus``

This file is responsible setting the user that is used to run the tomcat service, along
with the installation directory of the dispatcher service.

pinger
~~~~~~

Path:

  ``/services/dispatcher/bin/pinger``

The pinger file starts the pinger process associated with the dispatcher service. This is
identical to the pinger process being run with the monitor and worker services. It is
acceptable to run multiple pinger services.

context.xml
~~~~~~~~~~~

Path:

  ``/services/dispatcher/tomcat/webapps/ROOT/META-INF/context.xml``

This file controls how the dispatcher service connects to its associated databases:
provisioning and analytics.

mq.cfg
~~~~~~

Path:

  ``/services/dispatcher/tomcat/webapps/ROOT/WEB-INF/classes/mq.cfg``

This file controls how the dispatcher service connects to the mq service.

enstratus-km-client.cfg
~~~~~~~~~~~~~~~~~~~~~~~

Path:

  ``/services/dispatcher/tomcat/webapps/ROOT/WEB-INF/classes/enstratus-km-client.cfg``

This file controls the connection to the KM service by the dispatcher.

enstratus-provisioning.cfg
~~~~~~~~~~~~~~~~~~~~~~~~~~

Path:

  ``/services/dispatcher/tomcat/webapps/ROOT/WEB-INF/classes/enstratus-provisioning.cfg``

This file is a general control point for several items, the most important of which is the
encryption key for encrypting connections to the KM service.

dasein-persistence.properties
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Path:

  ``/services/dispatcher/tomcat/webapps/ROOT/WEB-INF/classes/dasein-persistence.properties``

This file defines the connection to the dasein persistence layer of enStratus. It also
specifies the connection point to the Riak database service.

server.xml
~~~~~~~~~~

Path:

  ``/services/dispatcher/tomcat/conf/server.xml``

The server.xml is responsible for controlling the start of the dispatcher service. This is
the place to change the listening and shutdown port of the dispatcher service.

enstratus-dispatcher
~~~~~~~~~~~~~~~~~~~~

Path:

  ``/etc/init.d/enstratus-dispatcher``

This file is the init script for starting/stopping the enStratus dispatcher service.

Logging Files
-------------

The enStratus dispatcher service logs to /services/dispatcher/tomcat/logs/catalina.out
