.. _workers:

Workers
=======

Worker Overview
----------------
The enStratus worker service consists of two components, a publisher and a subscriber. At a very high level,
these components:

1. Publisher

  - The publisher is responsible for pushing actions onto a queue. 

2. Subscriber

  - The subscriber is responsible for taking actions off of the queue and acting accordingly.

Starting Worker
---------------
To start the worker service:

.. code-block:: bash

	/etc/init.d/enstratus-workers start

Worker Start Process
~~~~~~~~~~~~~~~~~~~~~
The worker init script performs the following actions:

#. Executes /services/worker/bin/publisher, passing it the argument: start. This starts the publisher process.
#. Executes /services/worker/bin/subscriber, passing it the argument: start. This starts the subscriber process.

Stopping Worker
---------------
To stop the worker service:

.. code-block:: bash

	/etc/init.d/enstratus-workers stop

Configuration Files
-------------------

The enStratus workers service has 9 configuration files

.. hlist::
   :columns: 3

   * dasein-persistence.properties
   * enstratus-km-client.cfg
   * enstratus-provisioning.cfg
   * worker.properties
   * mq.cfg
   * pinger
   * worker
   * publisher
   * subscriber

worker
~~~~~~

Path:

  /services/worker/bin/worker

The worker file controls the start of a new worker process. 

Path:

  /services/worker/bin/pinger

The pinger file start the pinger process associated with the workers service. This is
identical to the pinger process being run with the dispatcher and monitor services. It is
acceptable to run multiple pinger services.


Path:

  /services/worker/bin/publisher

The publisher file controls the enStratus publisher process.

Path:

  /services/worker/bin/subscriber

The subscriber file controls the enStratus subscriber process.

Path:

  /services/worker/classes/dasein-persistence.properties

This file defines the connection to the dasein persistence layer of enStratus. It also
specifies the connection point to the Riak database service.


Path:

  /services/worker/classes/enstratus-km-client.cfg

Path:

  /services/worker/classes/enstratus-provisioning.cfg

Path:

  /services/worker/classes/worker.properties

Path:

  /services/worker/classes/mq.cfg

