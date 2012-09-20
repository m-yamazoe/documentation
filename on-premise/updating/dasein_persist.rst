.. _dasein_persist:

Dasein Persistence
------------------

Background
~~~~~~~~~~

The Dasein layer of enStratus is an open source cloud abstraction layer written and
maintained by enStratus Chief Technology Officer George Reese. For more information about
Dasein, please see the following:


`Dasein Persist <http://sourceforge.net/projects/dasein-persist/>`_

`Dasein on Sourceforge <http://dasein-cloud.sourceforge.net/>`_

`Dasein on GitHub <https://github.com/greese/dasein-cloud>`_

Updating
~~~~~~~~

There are two primary jars that implement the dasein functionality in enStratus. They are
referenced in the form:

#. dasein-persist-year.month.version.jar
#. dasein-util-year.month.version.jar

For example, you may be provided a jar called: 

``dasein-persist-2012.08.5.jar``

To update your enStratus environment to incorporate this new jar, do the following (adjust
as necessary for your jar version):

1. As root, shut down all enStratus services (order is not important)

KM
^^

.. code-block:: bash

   /etc/init.d/enstratus-km stop

dispatcher
^^^^^^^^^^

.. code-block:: bash

   /etc/init.d/enstratus-dispatcher stop

monitor
^^^^^^^

.. code-block:: bash

   /etc/init.d/enstratus-monitor stop

worker
^^^^^^

.. code-block:: bash

   /etc/init.d/enstratus-worker stop

console
^^^^^^^

.. code-block:: bash

   /etc/init.d/enstratus-console stop

API
^^^

.. code-block:: bash

   /etc/init.d/enstratus-api stop


2. Copy the new jar to the following lib directories and remove all existing dasein-persist
jars with older version numbers:

.. code-block:: bash

   /services/console/tomcat/webapps/ROOT/WEB-INF/lib/
   /services/api/tomcat/webapps/ROOT/WEB-INF/lib/
   /services/km/tomcat/webapps/ROOT/WEB-INF/lib/
   /services/dispatcher/tomcat/webapps/ROOT/WEB-INF/lib/
   /services/monitor/lib/
   /services/worker/lib/

.. warning:: Remember to chown enstratus:enstratus any files that are updated.

``chown enstratus:enstratus /services/console/tomcat/webapps/ROOT/WEB-INF/lib/dasein-persist-2012.08.5.jar``

``chown enstratus:enstratus /services/api/tomcat/webapps/ROOT/WEB-INF/lib/dasein-persist-2012.08.5.jar``

``chown enstratus:enstratus /services/km/tomcat/webapps/ROOT/WEB-INF/lib/dasein-persist-2012.08.5.jar``

``chown enstratus:enstratus /services/dispatcher/tomcat/webapps/ROOT/WEB-INF/lib/dasein-persist-2012.08.5.jar``

``chown enstratus:enstratus /services/monitor/lib/dasein-persist-2012.08.5.jar``

``chown enstratus:enstratus /services/worker/lib/dasein-persist-2012.08.5.jar``

3. Edit the following files:

.. code-block:: bash

   /services/console/tomcat/webapps/ROOT/WEB-INF/classes/dasein-persistence.properties
   /services/api/tomcat/webapps/ROOT/WEB-INF/classes/dasein-persistence.properties
   /services/dispatcher/tomcat/webapps/ROOT/WEB-INF/classes/dasein-persistence.properties
   /services/monitor/bin/poll
   /services/worker/classes/dasein-persistence.properties

Update the dasein.persist.persistenceLib line to reference the new jar
version. For example:

``dasein.persist.persistenceLib=/services/console/tomcat/webapps/ROOT/WEB-INF/lib/dasein-persist-2012.08.5.jar``

4. Delete the contents of the following tomcat work directories:

console
^^^^^^^

.. code-block:: bash

   sudo rm -rf /services/console/tomcat/work/*

API
^^^

.. code-block:: bash

   sudo rm -rf /services/api/tomcat/work/*

KM
^^

.. code-block:: bash

   sudo rm -rf /services/km/tomcat/work/*

Dispatcher
^^^^^^^^^^

.. code-block:: bash

   sudo rm -rf /services/dispatcher/tomcat/work/*

Monitor
^^^^^^^

.. code-block:: bash

   sudo rm -rf /services/monitor/work/*

5. As root, start all enStratus services (preferred order):

KM
^^

.. code-block:: bash

   /etc/init.d/enstratus-km start

dispatcher
^^^^^^^^^^

.. code-block:: bash

   /etc/init.d/enstratus-dispatcher start

monitor
^^^^^^^

.. code-block:: bash

   /etc/init.d/enstratus-monitor start

worker
^^^^^^

.. code-block:: bash

   /etc/init.d/enstratus-worker start

console
^^^^^^^

.. code-block:: bash

   /etc/init.d/enstratus-console start

API
^^^

.. code-block:: bash

   /etc/init.d/enstratus-api start

