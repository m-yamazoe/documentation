.. _saas_chef_credentials:

Generating credentials
----------------------

Before adding your Chef server to the enStratus console, you'll need two bits of information. This differs based on if you are running OSC vs. OHC/OPC.
Regardless of which type of Chef server product you are using, enstratus will need to be able to communicate with the Chef server API endpoint.
Additionally, enStratus will need an API client account and that account's PEM file. This single set of credentials will be used for the following tasks:

* Making API calls to the Chef server to read ``roles``, ``environments`` and ``cookbooks``.
* Bootstrapping new nodes via ``chef-client``
* Optionally, deleting node and client records after server termination

.. warning:: enStratus will NEVER alter any records on your Chef server. The logic simply
   doesn't exist in enStratus.  enStratus' access is read-only and only to the objects listed
   above. Please see the note below regarding ``admin`` access, however, if you'd like
   enStratus to take care of a few common termination-related tasks for you.

OSC
~~~

For OSC, you can either provide the existing validation credentials you currently use or
generate new credentials specifically for enStratus to use.  To generate custom
credentials for enStratus in OSC, you can use ``knife``:

``knife client create enstratus-validator -d -a``

::

	Created client[enstratus-validator]

	-----BEGIN RSA PRIVATE KEY-----
	MIIEoQIBAAKCAQEAvPSxK8lbC0ZAl+prCyQbR1Jv8hHj6vskK50V9H/r8N6YBUrN
	pftOiyvjzkb3zmfEaiUGKXoJxK4V2nzkU6qSvbQ/GOGUIZ+V3+t2YmdcvpudvVkA
	HbvIBZcbjmrQut/g61gFbLLw8rjQutc1Yad2Mdu1TBVgQVeBVGnnKVUus4iSYyAB
	16F236qP0N2IGidl1J1orCbAr2N/JuA0iwdXViX9onA5gZ7bSXcbP9FZRoN94gcb
	ZudgjelOBPIP7Qxfmfu/t9qhZzP8KyRUgpXpr0OVcDp48wHHVpPTMDajm+0gWIDq
	7N7Ze52mtzZGQ2LCd3d0SPIk5J51V3ROSgOqjQIDAQABAoIBAQCm5MU79Hw6yBEz
	XPSxAXIqm7COsaiKmsnGz9ddfkNKG4FQY1KigQZNvDVYs6waneKJEiyQI99O3agl
	s9wD3gwADJ0Sf+PTkt3Qymtk3QC4xkAbxuloWbyA24eWUdgMxsMlezhHWwGgkQaj
	kIPwvfWScgl+qv66l+x+P4/SHQ/Dt57H7jCjzqi20ViX+atH/8FoHTXJxwDuqu8D
	AYzMOqyL39TWvBxqa7ire4qx6OW0WBWlWLBYWrUO2RhOF5G+0vI2Uh0QwlJmyQdg
	WpEhLIS7yt1YwuSL/ph6fKcwM10dRAScRU35IQaMSYZV8/fbOOuYdFxFTCJHimp7
	A4G2gJdhAoGBAOleSqo64AlJWfgx5zB292zYwYlyzNNhCV5DeyHHdEzYCHJukvbc
	ch+UbUY3IXKz6MGA6Z5to7d0E5QE7JNotwQ0YBNbQzgZUxokU6oawix8vgelRqfL
	vwcn+6iTmgyyjyd82I+QaGD8vQ+i2/6vrgeetOnwJ6m2SMnuaJZQIi/VAoGBAM9H
	y6Zei+dnjykt5xnednvvu+Esp6+NBTZXpfG93f/+11vleF4lkwo6lwoTfkSZhbKh
	T8YpmATjhCtfI1XBf7LOwwzRfccH9dPUGO/gVswzpHMEDRGHVxwvJ83Jib04sakp
	hYqYvgd3gSuuDZNVqbACgTLCb3CwDLuKvKYdAyPZAn9Ci6C+6gr4mvIM1C4Yo9Pq
	NeT6TMIbhJAnURbLixSe1PuTpfRCcJoaZzjBzPa8vpCgnSIBC0KkDXWHv9+2KSYH
	DOhYnK2OUapgyfsRho/YH7oQdBCxyGeworYgW/aRqFkp6W/XgFZDUc6XptkUxwPZ
	KGhuTQ0CV/hpnJI2SqN1AoGAR1Fnk32SW3M5Qazmh/MQB0KL/UTVCUTXF0R+9zch
	rBPt21OP36zD89AG6dOdLVM5OiXggckL4hq5/gZE7RufqVEUsVNfGFz3ywN99QLW
	OnpGScCKEo7jfPIImviN6MoZ7p83sGEvePg4PGQtjZT6xnGGLIXTvA0GxHxOvkTb
	MLkCgYBSRpS2mc8yMH8K4PPMeIkogj8JL+gPrIvxHZypegcc1VuD7BCTZnVTb7eS
	ryChfYGSuFCMTrdJGzrcfC0AuBj0UpQVyq/VZxfnSGxjAZ9xmwnquP+c/QwiK9QV
	pN+rXdmloLy5YB4GXxiAFe/Yd4fYhTt6T33wY2V52y9qVra3lQ==
	-----END RSA PRIVATE KEY-----

Make note of the client name and also the private key returned to you.

.. note:: Note that we passed the ``-a`` option. This makes the enstratus account an
   administrator. This is optional but **HIGHLY** recommended.  If enStratus has permissions,
   it will clean up any node and client records for instances enStratus created. This is very
   valuable in cases where you are relying on search in your recipes as part of an enStratus
   deployment.  

   **Not only will enStratus never create any client or node records on its own
   but it will also NEVER delete any client or node records for anything OTHER than instances
   created from within enStratus**

OHC/OPC
~~~~~~~

For OHC/OPC, the finer grained ACL system doesn't allow you to create clients that have
the same permissions as OSC clients by default. For this you will have to do one of the
following

* Provide enStratus with the original validation cert and user-name or
* create a custom client as described above and then grant it the appropriate permissions

For creating a custom client, after creating the client with knife, log into the OPC/OHC
web UI and do the following:

* Create a group that you can easily identify as belonging to enStratus.
* Add the client previously created to this new group
* Add the new group to the ``admins`` group

As a side effect of these steps, your new client will now have the permissions it needs to
"clean up" any instances created by enStratus. This applies ONLY to instances created from
within enStratus. If you wish to grant the same permissions to your existing validation
client, simply follow the same steps for creating a custom group and adding it to the
``admins`` group.
