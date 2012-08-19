.. _monitors:

Monitors
========

The enStratus monitor service is a java service installed to /services/monitor. It is
composed of two components: assign and control.

Monitor Overview
----------------

.. note:: The enStratus monitors service is in the process of being deprecated in favor of
   a more efficient workers process. 

The enStratus monitors service is responsible for maintaining an accurate representation of cloud state,
checking on the completion of jobs initiated by the dispatcher service, orchestrating server launches and
service installations.

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

  /services/monitor/bin/assign

The assign file controls the start of each monitor process. Here is where adjustments to
the JAVA_OPTS used to start each monitor process can be made.

controller
~~~~~~~~~~

Path:

  /services/monitor/bin/controller

The controller file sets the parameters used to start the enStratus control process. Here
is where adjustmens to the JAVA_OPTS used to start the control process can be made.

monitor
~~~~~~~

Path:

  /services/monitor/bin/monitor

The monitor file goes through every monitor listed in the monitors.cfg file and starts
each monitor listed therein.

pinger
~~~~~~

Path:

  /services/monitor/bin/pinger

The pinger file start the pinger process associated with the monitors service. This is
identical to the pinger process being run with the dispatcher and worker services. It is
acceptable to run multiple pinger services.

enstratus-km-client.cfg
~~~~~~~~~~~~~~~~~~~~~~~

Path:

  /services/monitor/classes/enstratus-km-client.cfg

This file controls the connection to the KM service by the monitors. 

enstratus-provisioning.cfg
~~~~~~~~~~~~~~~~~~~~~~~~~~

Path:

  /services/monitor/classes/enstratus-provisioning.cfg

This file is a general control point for several items, the most important of which is the
encryption key for encrypting connections to the KM service. This is also where a setting
called SOURCE_CIDR is made, which specifies IP addresses from which enStratus will make
connections to guest VM.

dasein-persistence.properties
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Path:

  /services/monitor/etc/dasein-persistence.properties

This file defines the connection to the dasein persistence layer of enStratus. It also
specifies the connection point to the Riak database service.

mq.cfg
~~~~~~

Path:

  /services/monitor/classes/mq.cfg

This file controls how the monitor service connects to the mq service.

cloud.properties
~~~~~~~~~~~~~~~~

Path:

  /services/monitor/etc/cloud.properties

The cloud.properties file is used to define the connection points for the monitor service
to connect to the provisioning and analytics MySQL databases.

monitors.cfg
~~~~~~~~~~~~

Path:

  /services/monitor/etc/monitors.cfg

The is file is used to specify which of the enStratus monitors are started during the
start process. This file is read by the assign process.
