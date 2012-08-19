Console
=======

The enStratus console is a tomcat service installed to /services/console.

Console Overview
----------------
The enStratus console service is a tomcat service that provides the web front-end, or enStratus user console.

Starting Console
----------------
To start the console service:

.. code-block:: bash

	/etc/init.d/enstratus-console start

Console Start Process
~~~~~~~~~~~~~~~~~~~~~
The console init script performs the following action:

#. Executes /services/console/bin/tomcat, passing it the argument: start. This starts the console process.

Stopping Console
----------------
To stop the console service:

.. code-block:: bash

	/etc/init.d/enstratus-console stop

Console Stop Process
~~~~~~~~~~~~~~~~~~~~
The console init script performs the following action:

#. Executes /services/console/bin/tomcat, passing it the argument: stop. This stops the console process.

Configuration Files
-------------------

The enStratus console service has 7 configuration files

.. hlist::
   :columns: 3

   * tomcat
   * enstratus
   * context.xml
   * enstratus-webservices.cfg
   * dasein-persistence.properties
   * enstratus-console.cfg
   * custom/networks.cfg

tomcat
~~~~~~

Path:

  /services/console/bin/tomcat

This file is responsible for controlling the start of the console service. Any
JAVA_OPTS that need to be passed to the console tomcat service can be done using this
file.

enstratus
~~~~~~~~~

Path:

  /services/console/bin/enstratus

This file is responsible setting the user that is used to run the tomcat service, along
with the installation directory of the console service.

context.xml
~~~~~~~~~~~

Path:

  /services/console/tomcat/webapps/ROOT/META-INF/context.xml

This file controls how the console service connects to its associated databases:
console and enstratus_console.

enstratus-webservices.cfg
~~~~~~~~~~~~~~~~~~~~~~~~~

Path:

  /services/console/tomcat/webapps/ROOT/WEB-INF/classes/enstratus-webservices.cfg

This file defines the webservices endpoints for the console service to connect to the
enStratus dispatcher service.

dasein-persistence.properties
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Path:

  /services/console/tomcat/webapps/ROOT/WEB-INF/classes/dasein-persistence.properties

This file defines the connection to the dasein persistence layer of enStratus. It also
specifies the connection point to the Riak database service.

enstratus-console.cfg
~~~~~~~~~~~~~~~~~~~~~

Path:

  /services/console/tomcat/webapps/ROOT/WEB-INF/classes/enstratus-console.cfg

This file is used to define the url to which the console will respond.

custom/networks.cfg
~~~~~~~~~~~~~~~~~~~

Path:

  /services/console/tomcat/webapps/ROOT/WEB-INF/classes/custom/networks.cfg

This file is a general control point for several items, the most important of which is the
encryption key for encrypting connections to the dispatcher web services.
