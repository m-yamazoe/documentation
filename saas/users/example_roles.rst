.. _saas_example_roles:

Example Roles
-------------

The following four roles are examples you can implement in your enStratus account. The Admin role is created in 
every enStratus account by default. You can customize these roles or create your own.


**Admin** - Has full access over the entire system.

.. tabularcolumns:: |l|l|l|

+----------+--------+-----------+
| Resource | Action | Qualifier |
+==========+========+===========+
| ANY      | ANY    | ANY       |
+----------+--------+-----------+

|

**CloudManager** - Can manage all aspects of a cloud environment, but no account management.

.. tabularcolumns:: |l|l|l|

+--------------+--------+-----------+
| Resource     | Action | Qualifier |
+==============+========+===========+
| CONSOLE      | Access | ANY       |
+--------------+--------+-----------+
| CLUSTER      | ANY    | ANY       |
+--------------+--------+-----------+
| DISTRIBUTION | ANY    | ANY       |
+--------------+--------+-----------+
| FIREWALL     | ANY    | ANY       |
+--------------+--------+-----------+
| IMAGE        | ANY    | ANY       |
+--------------+--------+-----------+
| IP           | ANY    | ANY       |
+--------------+--------+-----------+
| LB           | ANY    | ANY       |
+--------------+--------+-----------+
| SERVER       | ANY    | ANY       |
+--------------+--------+-----------+
| SNAPSHOT     | ANY    | ANY       |
+--------------+--------+-----------+
| VOLUME       | ANY    | ANY       |
+--------------+--------+-----------+

|

**Configurator** - Can edit configurational elements that have no economic impact.

.. tabularcolumns:: |l|l|l|

+--------------+-----------+-----------+
| Resource     | Action    | Qualifier |
+==============+===========+===========+
| CONSOLE      | Access    | ANY       |
+--------------+-----------+-----------+
| CLUSTER      | Configure | ANY       |
+--------------+-----------+-----------+
| DISTRIBUTION | Configure | ANY       |
+--------------+-----------+-----------+
| FIREWALL     | Configure | ANY       |
+--------------+-----------+-----------+
| IMAGE        | Configure | ANY       |
+--------------+-----------+-----------+
| IP           | Configure | ANY       |
+--------------+-----------+-----------+
| LB           | Configure | ANY       |
+--------------+-----------+-----------+
| SERVER       | Configure | ANY       |
+--------------+-----------+-----------+
| SNAPSHOT     | Configure | ANY       |
+--------------+-----------+-----------+
| VOLUME       | Configure | ANY       |
+--------------+-----------+-----------+

|

**CSR** - Has read-only access to the entire system.

.. tabularcolumns:: |l|l|l|

+----------+--------+-----------+
| Resource | Action | Qualifier |
+==========+========+===========+
| CONSOLE  | Access | ANY       |
+----------+--------+-----------+

