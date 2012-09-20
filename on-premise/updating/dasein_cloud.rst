.. _dasein_cloud:

Dasein Cloud
------------

Background
~~~~~~~~~~

The dasein cloud abstraction layer is at the heart of the enStratus provisioning and
management systems. The exists a Java jar for each of the clouds, in addition to the
dasein-persist and dasein-util jars. The current list of cloud jars is:

   * dasein-cloud-aws-2012.04.4.jar
   * dasein-cloud-cloudstack-2012.04.2.jar
   * dasein-cloud-core-2012.04.2.jar
   * dasein-cloud-euca-2012.04.2.jar
   * dasein-cloud-flexiscale-2012.04.jar
   * dasein-cloud-google-2012.04.jar
   * dasein-cloud-joyent-2012.04.3.jar
   * dasein-cloud-nimbula-2012.04.jar
   * dasein-cloud-nova-2012.04.4.jar
   * dasein-cloud-opsource-2012.04.2.jar
   * dasein-cloud-rackspace-2012.04.jar
   * dasein-cloud-vsphere-2012.04.1.jar
   * dasein-jclouds-atmos-2012.04.jar
   * dasein-jclouds-azure-2012.04.jar
   * dasein-jclouds-cloudsigma-2012.04.jar
   * dasein-jclouds-gogrid-2012.04.jar
   * dasein-jclouds-rackspace-2012.04.jar
   * dasein-jclouds-softlayer-2012.04.jar
   * dasein-jclouds-terremark-2012.04.jar
   * dasein-jclouds-vcloud-2012.04.jar

Updates to these jars can be found at:


`Maven Central <http://search.maven.org>`_

For example, if you wanted to find the latest cloudstack jar, you could search:

`CloudStack <http://search.maven.org/#search%7Cga%7C1%7Cdasein-cloud-cloudstack>`_

And download it from there.

Updating
~~~~~~~~

Updating dasein cloud jar files is less involved than updating dasein-persist or
dasein-util. To update follow these steps:

1. Locate existing jars. To find the existing jars, issue a command similar to:

.. code-block:: bash

   # find /services -type f -iname "*dasein-cloud-cloudstack*"
   /services/dispatcher/tomcat/webapps/ROOT/WEB-INF/lib/dasein-cloud-cloudstack-2012.04.2.jar
   /services/monitor/lib/dasein-cloud-cloudstack-2012.04.2.jar
   /services/worker/lib/dasein-cloud-cloudstack-2012.04.2.jar

In this case, we find 3 jars for cloudstack. 

2. Save one copy of the old jar for rollback purposes, and **remove** the old jars from the
   directories in which they currently reside.

3. Deploy the new jar. Remember to: 

   ``chown enstratus:enstratus dasein-cloud-cloudstack-2012.04.2.jar``

4. Restart any affected services, in this example, restarting the dispatcher, monitor, and
   worker services is required.

Database Changes
~~~~~~~~~~~~~~~~

For minor updates, there are typically not any associated database schema changes,
however, you should consult an enStratus engineer before deploying new jars to your
environment.
