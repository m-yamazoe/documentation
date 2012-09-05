.. _installation:

Installation
------------

enStratus engineers are working hard to simplify the enStratus cloud management software
process for proof-of-concept environments.

Here is reference material for installing enStratus in a 1, 2, and 4 server model.

.. warning:: Installation in a multi-node environment cannot resolve "external" issues
   such as firewalls that may separate nodes and DNS. If in doubt of your DNS situation or
   your ability to edit /etc/hosts files, please use IP Addresses in lieu of hosts names
   where applicable.

.. toctree::
   :maxdepth: 2
   :hidden:

   single_node/single_node
   two_node/two_node
   n_node/n_node
   four_node/four_node
   single_node/post_install
