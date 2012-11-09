Credentials
-----------

The credentials option in the actions menu is to allow credentials or other private information to be passed directly to the running instance.

.. figure:: ./images/credentials0.jpg
   :height: 290px
   :width: 1300 px
   :scale: 55 %
   :alt: MySQL, Credentials
   :align: center

Click '+ Add Credentials'.

.. figure:: ./images/credentials1.png
   :height: 465px
   :width: 612 px
   :scale: 75 %
   :alt: MySQL: Credentials
   :align: center

You need to fill MySQL credential configuration properly to initialize MySQL database.

.. figure:: ./images/credentials2.png
   :height: 461 px
   :width: 610 px
   :scale: 75 %
   :alt: MySQL: Credentials
   :align: center

.. list-table::
   :widths: 30 22 90
   :header-rows: 1

   * - Field
     - Value
     - Description
   * - Name
     - admin
     - You must use 'admin'. This name is used in enstratus service configuration scripts. This field won't be configurable in next update.
   * - Public Key Part
     - (username)
     - Any username you want. This field name must sound strange but this is the username for initializing MySQL database.
   * - Private Key Part
     - (password)
     - Any password you want.
|
After configuring credential, you will see the credential on the list.

.. figure:: ./images/credentials3.png
   :height: 460 px
   :width: 611 px
   :scale: 75 %
   :alt: MySQL: Credentials
   :align: center
