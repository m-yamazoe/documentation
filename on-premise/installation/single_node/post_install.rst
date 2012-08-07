Single-Node Post Install
------------------------

Once the installation successfully completes, it's time to start the enStratus software.
Do this in the following order:

1. /etc/init.d/enstratus-km start
  
   Before proceeding to the next step, check to make sure the KM service is running. Check
   the file `/services/km/tomcat/logs/catalina.out` for any errors.

2. /etc/init.d/enstratus-dispatcher start

   Check the file `/services/dispatcher/tomcat/logs/catalina.out`. You should see a
   successful detection of your license key near the top, and a successful process start.

3. /etc/init.d/enstratus-console start

   Check the file `/services/console/tomcat/logs/catalina.out`. You may seem some warnings
   here, and so long as the service eventually starts, all should be well.

.. note:: Experienced installers of enStratus will note that we're **not** starting the
   enStratus workers and monitors services. Those can be started now if you so desire, but
   are not required until after successful registration.

4. Register. In your browser, navigate to https://cloud.mycompany.com/page/1/register.jsp,
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

5. /etc/init.d/enstratus-workers start

6. /etc/init.d/enstratus-monitor start

   You will see a scrolling list of monitors ticking by.

7. Return to the browser to enter your cloud credentials for the cloud provider of your
   choosing.

