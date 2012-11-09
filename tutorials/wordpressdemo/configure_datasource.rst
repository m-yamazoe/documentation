Data Source
-----------

The Datasource options in actions menu for services allows the application designer to
associate a starting datasource with a database service. On the MySQL service, associate
the datasource uploaded earlier as shown:

The MySQL service is special, enStratus will execute a routine to install a datasource
once the mysql service is configured. On the MySQL service, choose actions > Data Sources

.. figure:: ./images/addDataSource0.png
   :height: 500px
   :width: 1900 px
   :scale: 50 %
   :alt: Service, Data Source
   :align: center

Click '+ Add Data Source'.

.. figure:: ./images/addDataSource1.png
   :height: 300px
   :width: 800 px
   :scale: 50 %
   :alt: Service, Data Source
   :align: center

You need to fill information in Data Sources configuration dialogue window.

.. figure:: ./images/addDataSource2.png
   :height: 700px
   :width: 900 px
   :scale: 50 %
   :alt: Service, Data Source
   :align: center
.. list-table::
   :widths: 35 22 95
   :header-rows: 1

   * - Field
     - Value
     - Description
   * - Service User
     - (username)
     - This will be used by wordpress service to connect to its MySQL database. It doesn't need to be the same username as configured in credential.
   * - Service Password
     - (password)
     - Any password you want.
|
If configured properly, you will see the data source from the list.

.. figure:: ./images/addDataSource3.png
   :height: 300px
   :width: 850 px
   :scale: 50 %
   :alt: Service, Data Source
   :align: center
