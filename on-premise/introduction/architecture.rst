Architecture Requirements
-------------------------

.. figure:: images/enstratusArchitecture.png
   :height: 600px
   :width: 800 px
   :scale: 75 %
   :alt: 4-server Architecture
   :align: center

   4-server Architecture

To install enStratus on 4-5 machines
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tabularcolumns:: |l|l|l|l|l|

+----------------+---------------+-------------+--------------+----------------------------------+
| Component      | Compute Cores | Memory (Gb) | Storage (Gb) | Software                         |
+================+===============+=============+==============+==================================+
| Key Management | 1             | 1           | 10           | Sun Java JDKv6, MySQL            |
+----------------+---------------+-------------+--------------+----------------------------------+
| Dispatcher     | 2-4           | 4-6         | 20           | Sun Java JDKv6, MySQL, Rabbit MQ |
+----------------+---------------+-------------+--------------+----------------------------------+
| Monitor/Worker | 2-4           | 6-8         | 20           | Sun Java JDKv6, MySQL, JSVC      |
+----------------+---------------+-------------+--------------+----------------------------------+
| Console        | 1-2           | 2-4         | 20           | Sun Java JDKv6                   |
+----------------+---------------+-------------+--------------+----------------------------------+

To install enStratus on 2 machines
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tabularcolumns:: |l|l|l|l|l|

+----------------+---------------+-------------+--------------+----------------------------------------+
| Component      | Compute Cores | Memory (Gb) | Storage (Gb) | Software                               |
+================+===============+=============+==============+========================================+
| Backend        | 4-6           | 6-12        | 30           | Sun Java JDKv6, MySQL, Rabbit MQ, JSVC |
+----------------+---------------+-------------+--------------+----------------------------------------+
| Console        | 4-6           | 6-12        | 30           | Sun Java JDKv6, MySQL                  |
+----------------+---------------+-------------+--------------+----------------------------------------+

.. Note:: In the case of the 2-server model, the KM, Dispatcher, Monitor, and Worker services will be installed on one machine. 

To install enStratus on a single machine
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tabularcolumns:: |l|l|l|l|l|

+----------------+---------------+-------------+--------------+----------------------------------------+
| Component      | Compute Cores | Memory (Gb) | Storage (Gb) | Software                               |
+================+===============+=============+==============+========================================+
| Everything     | 6-24          | 6-12        | 30           | Sun Java JDKv6, MySQL, Rabbit MQ, JSVC |
+----------------+---------------+-------------+--------------+----------------------------------------+


.. Warning:: Installing with the bare minimum requirements may result in a degradation of performance.


