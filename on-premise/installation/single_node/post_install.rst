Post Install
------------

MySQL
~~~~~

The root password for MySQL was generated at the time of installation. The password will
be located in ``/etc/mysql/grants.sql`` on Debian derivatives and
``/etc/mysql_grants.sql`` on RHEL and CentOS.

Helpful Aliases
~~~~~~~~~~~~~~~

If you have completed a single-node install of enStratus, we have included for you a
helpful set of aliases for starting and stopping enStratus services as well as shortcuts
for viewing logs.

On a single-node install, all of these will work. On a multi-node installation, only the
aliases that reference installed components will apply. 

.. code-block:: bash

   # KM Aliases
   alias startKM='/etc/init.d/enstratus-km start'
   alias stopKM='/etc/init.d/enstratus-km stop'
   alias lessKM='less /services/km/tomcat/logs/catalina.out'
   alias tailKM='tail -f /services/km/tomcat/logs/catalina.out'
   
   # Dispatcher Aliases
   alias startDispatcher='/etc/init.d/enstratus-dispatcher start'
   alias stopDispatcher='/etc/init.d/enstratus-dispatcher stop'
   alias tailDispatcher='tail -f /services/dispatcher/tomcat/logs/catalina.out'
   alias lessDispatcher='less /services/dispatcher/tomcat/logs/catalina.out'
   
   # Console Aliases
   alias startConsole='/etc/init.d/enstratus-console start'
   alias stopConsole='/etc/init.d/enstratus-console stop'
   alias tailConsole='tail -f /services/console/tomcat/logs/catalina.out'
   alias lessConsole='less /services/console/tomcat/logs/catalina.out'
   
   # API Aliases
   alias startApi='/etc/init.d/enstratus-api start'
   alias stopApi='/etc/init.d/enstratus-api stop'
   alias tailApi='tail -f /services/api/tomcat/logs/catalina.out'
   alias lessApi='less /services/api/tomcat/logs/catalina.out'
   
   # Monitor Aliases
   alias startMonitors='/etc/init.d/enstratus-monitor start'
   alias stopMonitors='/etc/init.d/enstratus-monitor stop'
   
   # Worker Aliases
   alias startWorkers='/etc/init.d/enstratus-workers start'
   alias stopWorkers='/etc/init.d/enstratus-workers stop'

To access these aliases: issue the following command when you are the root user:

.. code-block:: bash

   # source ~/.bashrc

or

.. code-block:: bash

   # . ~/.bashrc

if you prefer

Now, you can start and tail the dispatcher logs in one step by combining:

.. code-block:: bash

   # startDispatcher && tailDispatcher

Take care to not run this command multiple times in that form, or else you will get
multiple dispatcher processes running.

If the dispatcher is already running, simply issue:

.. code-block:: bash

   # tailDispatcher

to start tailing the log.

If you just want to **view** the dispatcher log, issue the command:

.. code-block:: bash

   # lessDispatcher

and the unix program called ``less`` will be used to open the dispatcher log.

enStratus Services
~~~~~~~~~~~~~~~~~~

Once the installation successfully completes, it's time to start the enStratus services.
Do this in the following order:

**1. Start the Key Management service:**

.. code-block:: bash

  /etc/init.d/enstratus-km start

Check the file `/services/km/tomcat/logs/catalina.out` for any errors.

Before proceeding to the next step, make sure the KM service is running. 
You should see a java service running on port 2013:

.. code-block:: bash

  netstat -tnlup | grep 2013
  tcp6       0      0 :::2013                 :::*                    LISTEN 7159/java  

**2. Start the Dispatcher service:**

.. code-block:: bash

  /etc/init.d/enstratus-dispatcher start

Check the file `/services/dispatcher/tomcat/logs/catalina.out`. You should see a
successful detection of your license key near the top. Check to make sure the Dispatcher 
service is running - there should be a java service on port 3302:

.. code-block:: bash

  netstat -tnlup | grep 3302
  tcp6       0      0 :::3302                 :::*                    LISTEN 7199/java  

**3. Start the Console service:**

.. code-block:: bash
   
   /etc/init.d/enstratus-console start

Check the file `/services/console/tomcat/logs/catalina.out`. You may seem some warnings
here, but so long as the service eventually starts, all should be well.

.. note:: Experienced installers of enStratus will note that we're **not** starting the
   enStratus workers and monitors services. Those can be started now if you so desire, but
   are not required until after successful registration.

**4. Register:** 

In your browser, navigate to https://cloud.mycompany.com/page/1/register.jsp,
replacing cloud.mycompany.com with the url you specified in in your attributes file.

Successful registration will direct the user to a page where cloud credentials can be
entered. Before proceeding, let's start the worker and monitor services.

.. figure:: ./images/register.png
   :height: 700px
   :width: 900 px
   :scale: 55 %
   :alt: Registration
   :align: center

   Registration

**5. Start the Workers:**

.. code-block:: bash

   /etc/init.d/enstratus-workers start

**6. Start the Monitors:**

.. code-block:: bash

 /etc/init.d/enstratus-monitor start

You will see a scrolling list of monitors ticking by.

**7. Enter Cloud Credentials:**

Return to the browser to enter your cloud credentials for the cloud provider of your
choosing.

