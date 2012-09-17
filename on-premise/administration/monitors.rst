.. _monitors:

Monitors
========

The enStratus monitor service is a java service installed to /services/monitor. It is
composed of two components: assign and control.

.. figure:: ./images/monitorWorker.png
   :height: 300 px
   :width: 450 px
   :scale: 70 %
   :alt: Monitors/Workers Service
   :align: center

   Monitors/Workers Service Connections

Overview
--------

The enStratus monitors service is responsible for maintaining an accurate representation
of cloud state, checking on the completion of jobs initiated by the dispatcher service,
orchestrating server launches and service installations.

The relationship of the enStratus monitors to the rest of the enStratus cloud management
system is shown in the above diagram.

The monitors service does not listen for incoming connections.

The purpose of the monitor system of enStratus is to, well, monitor cloud state and
accommodate changes that happen both inside of the enStratus cloud management system and
discover changes that happen outside of enStratus, for example when a user initiates an
action in the cloud provider console.

Installation
------------

Installation of the enStratus monitor service is best handled by using a configuration
management system such as Chef or Puppet.

Software Requirements
---------------------

The monitor service depends on the Java 6 Java Development Kit (JDK) provided by Oracle,
along with the associated Java Cryptographic Extensions (JCE). The jsvc is also required.

The JDK is installed to /usr/local/jdk and the jce jars are installed to
/usr/local/jdk/security/lib. JSVC is typically installed to /usr/bin/jsvc or
/usr/sbin/jsvc.

Incoming Connections
--------------------

None.


Outgoing Connections
--------------------

#. Cloud API

   The monitor service connects to the cloud provider API to discover changes, track the
   completion of jobs initiated by the dispatcher, and maintain cloud state.

#. Riak and MySQL

   The monitor service connects to the Riak and MySQL databases for the purposes of pushing
   state information. enStratus monitors also connect directly to the cloud provider API and
   poll to discover changes. 
   
   The polling interval for each enStratus monitor varies depending on the cloud service
   being monitored. Cloud resources that require up-to-the-minute monitoring, for example
   when servers are started, will be polled more rapidly than less critical resources such as
   the creation of a snapshot, or the deletion of a volume.

#. Guest VM

   The monitor service will also poll guest VM running in the cloud for the purpose of
   collecting logs.

Starting Monitor
----------------

To start the monitor services:

.. code-block:: bash

	/etc/init.d/enstratus-monitor start

Monitor Start Process
~~~~~~~~~~~~~~~~~~~~~

The monitor init script performs the following actions:

#. Executes /services/monitor/bin/assign. This starts the assignment service, which is
   responsible for controlling the monitor services.

#. The monitor start process cycles through a list of monitors designated in the file
   called /services/monitor/etc/monitors.cfg, executing a call to /services/monitor/bin/poll,
   with the start argument, as shown. Each monitor process has an associated log file located
   in /services/monitor/log.

.. code-block:: bash

	Starting assign
	Starting assignment...
	Started 0.
	Starting monitors.
	/services/monitor/bin/poll start Address 1
	/services/monitor/bin/poll start AutoScaling 1
	/services/monitor/bin/poll start Backup 1
	/services/monitor/bin/poll start Budget 1
	/services/monitor/bin/poll start Dc 1
	/services/monitor/bin/poll start Deployment 1
	/services/monitor/bin/poll start DeploymentAnalytics 1
	/services/monitor/bin/poll start Distribution 1
	/services/monitor/bin/poll start Dns 1
	/services/monitor/bin/poll start ExchangeRate 1
	/services/monitor/bin/poll start ForwardingRule 1
	/services/monitor/bin/poll start Image 1
	/services/monitor/bin/poll start Invoice 1
	/services/monitor/bin/poll start KeyValue 1
	/services/monitor/bin/poll start LoadBalancer 1
	/services/monitor/bin/poll start Maintenance 1
	/services/monitor/bin/poll start Notifications 1
	/services/monitor/bin/poll start Prepayment 1
	/services/monitor/bin/poll start Rdbms 1
	/services/monitor/bin/poll start ScalingEvent 1
	/services/monitor/bin/poll start ScalingEventProcess 1
	/services/monitor/bin/poll start Server 1
	/services/monitor/bin/poll start ServerAnalytics 1
	/services/monitor/bin/poll start Snapshot 1
	/services/monitor/bin/poll start Ssl 1
	/services/monitor/bin/poll start Subscription 1
	/services/monitor/bin/poll start TierAnalytics 1
	/services/monitor/bin/poll start Volume 1
	/services/monitor/bin/poll start VPNGateway 1

Stopping Monitor
----------------
To stop the monitor services:

.. code-block:: bash

	/etc/init.d/enstratus-monitor stop

Monitor Stop Process
~~~~~~~~~~~~~~~~~~~~

The monitor init script performs the following actions:

#. Executes /services/dispatcher/bin/assign, passing the stop argument. This stops the assignment service.

#. The monitor start process cycles through a list of monitors designated in the file
   called /services/monitor/etc/monitors.cfg, executing a call to /services/monitor/bin/poll,
   with the stop argument, as shown. Each monitor process has an associated log file located
   in /services/monitor/log.

.. note:: The monitor stop process is slow and not terribly reliable. A less elegant, yet faster method for
	 terminating the monitor processes is to issue the command:

	 ps -ef | grep onit | awk '{print $2}' | while read line; do kill -9 $line; done

Running Monitors
----------------

In a high demand environment, or in environments where the amount of resources per server
is limited, it is possible to split up the monitors that run on any given machine. For
example, you may want to run high-activity monitors like the server or image monitor on a
separate machine.

The downside of doing this is that it introduces unique server in your deployed enStratus
environment and can add management complexity.

It's also possible to run multiple monitor processes on the same server or on multiple
servers. For example, to separate hosts can be running the server monitor. This can be
done for any of the monitor services.

To adjust what monitors start/stop, simply edit the file
/services/monitor/etc/monitors.cfg and remove any monitors that aren't appropriate or are
perhaps running on another host. For example, if the cloud providers you are managing with
enStratus do not have a concept of block storage devices, you may want to disable the
unnecessary volume and snapshot monitors.

Individual monitor processes are not in and of themselves critical. Not having a monitor
running will result in changes not being picked up and displayed in the enStratus console.
Starting the monitor will cause the monitor to poll and discover any changes that have
occurred. The monitors are stateless, and can be stopped and started at any time.

Logging
-------

Each enStratus monitor has a dedicated logfile associated with it located in
/services/monitor/log. Log levels are controlled by
/services/monitor/classes/log4j.xml or log4j.properties.

Monitoring
----------

Backups
-------

Service
~~~~~~~

The enStratus monitor service files should be backed up before and after any changes, and
once/day during steady-state operations. Backups should be performed on
/services/monitor.

An example of how to backup the monitor service is shown here, in this case excluding the
log directory.

.. code-block:: bash

   #!/bin/bash
   
   TAR=/bin/tar
   GZIP=/bin/gzip
   
   DIR=/var/enstratus/backups
   BASE=monitors
   DA=`date +%Y%m%d-%H%M%S`
   
   FILE=${DIR}/${BASE}-${DA}.tar.gz
   
   find ${DIR} -type f -iname "*.gz" -mtime +2 | xargs rm -f
   
   cd /services/monitor/
   $TAR -czf ${FILE}  --exclude='work/*' --exclude='log/*' . > /dev/null 2>&1
   chmod 700 ${FILE}

Databases
~~~~~~~~~

The enStratus monitor service depends on the provisioning and analytics databases along
with the enStratus dispatcher service. Backups of these database are discussed in the
dispatcher service section.

Configuration Files
-------------------

The enStratus monitors service has 10 configuration files

.. hlist::
   :columns: 2

   * assign
   * controller
   * monitor
   * pinger
   * enstratus-km-client.cfg
   * enstratus-provisioning.cfg
   * mq.cfg
   * cloud.properties
   * monitors.cfg

assign
~~~~~~

Path:

  ``/services/monitor/bin/assign``

The assign file controls the start of each monitor process. Here is where adjustments to
the JAVA_OPTS used to start each monitor process can be made.

controller
~~~~~~~~~~

Path:

  ``/services/monitor/bin/controller``

The controller file sets the parameters used to start the enStratus control process. Here
is where adjustmens to the JAVA_OPTS used to start the control process can be made.

monitor
~~~~~~~

Path:

  ``/services/monitor/bin/monitor``

The monitor file goes through every monitor listed in the monitors.cfg file and starts
each monitor listed therein.

pinger
~~~~~~

Path:

  ``/services/monitor/bin/pinger``

The pinger file start the pinger process associated with the monitors service. This is
identical to the pinger process being run with the dispatcher and worker services. It is
acceptable to run multiple pinger services.

enstratus-km-client.cfg
~~~~~~~~~~~~~~~~~~~~~~~

Path:

  ``/services/monitor/classes/enstratus-km-client.cfg``

This file controls the connection to the KM service by the monitors. 

enstratus-provisioning.cfg
~~~~~~~~~~~~~~~~~~~~~~~~~~

Path:

  ``/services/monitor/classes/enstratus-provisioning.cfg``

This file is a general control point for several items, the most important of which is the
encryption key for encrypting connections to the KM service. This is also where a setting
called SOURCE_CIDR is made, which specifies IP addresses from which enStratus will make
connections to guest VM.

dasein-persistence.properties
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Path:

  ``/services/monitor/etc/dasein-persistence.properties``

This file defines the connection to the dasein persistence layer of enStratus. It also
specifies the connection point to the Riak database service.

mq.cfg
~~~~~~

Path:

  ``/services/monitor/classes/mq.cfg``

This file controls how the monitor service connects to the mq service.

cloud.properties
~~~~~~~~~~~~~~~~

Path:

  ``/services/monitor/etc/cloud.properties``

The cloud.properties file is used to define the connection points for the monitor service
to connect to the provisioning and analytics MySQL databases.

monitors.cfg
~~~~~~~~~~~~

Path:

  ``/services/monitor/etc/monitors.cfg``

The is file is used to specify which of the enStratus monitors are started during the
start process. This file is read by the assign process.
