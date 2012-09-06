.. _saas_puppet_espm:

ESPM
=====

Before enStratus can be used with a Puppet Enterprise 2.5 server, it is necessary to have
a small agent running on the server.

Why an agent?
-------------

To understand why an agent is needed, it's important to understand a bit about how
enStratus chooses to interact with CM systems.  One of the primary goals when initially
adding support for third-party CM systems to enStratus was that enStratus allowed you to
use your CM system as the "source of truth" about a system's configuration. enStratus is
largely unopinionated in the matter. While we have our own CM system (ObjectStore), we
understand that it cannot meet the needs of all users and that asking users to switch to
OUR configuration management system would be wrong.

Another goal is that you would not be forced to use enStratus to interact with your CM
system. You should not need to do anything special for enStratus to use your CM system and
you shouldn't have to jump through hoops to disconnect enStratus from your CM system. The
nature of CM systems is that they likely aren't just being used for cloud resources via
enStratus.

The final goal is that you should be able to manage the configuration of your systems
using the tools provided by your CM product.

When we looked at the various approaches for integrating enStratus with Puppet, we had
several choices in front of us:

- Force you to use enStratus as an ENC
- Integrate enStratus with Hiera
- Use the unstable puppet-dashboard REST API
- Use the enStratus agent

The first option was eliminated quickly as it violated all of the goals above. The second
option would only provide limited integration.  The third option was appealing but due to
the volatility of the puppet-dashboard HTTP API (and lack of certain functionality), it
was ruled out.

That leaves us with using the enStratus agent. The problem here is that the enStratus
agent is designed to work specifically with cloud instances. It is highly likely that
users would not even have the Puppet Master managed by enStratus. The only remaining
option, and one that has been suggested by Puppet Labs, was to create a custom agent with
very specific set of functionality. That's what ``espm`` is.

Internals
---------

``espm`` is a very small lightweight Python daemon. It requires Python 2.6 (due to some
internal dependencies) and leverages the PE2.5 puppet-dashboard rake tasks as well as the
native puppet ``cert`` face.

``espm`` has been tested on the following distributions:

* Ubuntu 10.04-12.04
* CentOS 5 (with python26 from the EPEL repository)

Specifically for each distro:

Ubuntu
~~~~~~

* build-essential
* python-setuptools
* python-dev

CentOS5
~~~~~~~

* openssl-devel
* gcc
* gcc-c++
* python26 (from EPEL)
* python26-devel (from EPEL)
* python26-distribute


``espm`` exposes an HTTP interface that enStratus uses to perform the following tasks:

* List groups and classes
* Add nodes to the PE2.5 ENC
* Assign groups, classes and parameters to nodes it creates
* Delete nodes that it creates
* Use native puppet commands to generate and revoke client certificates

``espm`` will never touch any existing node and you cannot use it to create new classes or
groups. It can't even list existing nodes. The functionality isn't exposed. The only
reason ``espm`` exists is so that you can perform automation within enStratus and use
Puppet to configure the systems in the same automated fashion.

``ESPM`` Installation
---------------------

Currently ``espm`` is made available on request to enStratus customers. The reason for
this is so that we want to be aware so we can ensure that we provide the best support
possible during the integration. Requesting ``espm`` is simply a matter of opening a
support ticket within enStratus.

Assuming you have the prerequisites for your distro above installed and have extracted
``espm`` to ``/usr/src/espm``, you would run:

``python setup.py install`` (for Ubuntu)
or
``python26 setup.py install`` (for CentOS5-EPEL)

This will pull the appropriate dependencies and install ``espm``.

``ESPM`` Setup
--------------

Two commands will now be available to you:

* espm
* espm_setup

``espm_setup`` has full help output but the primary options you'll need to worry about are:

* ``-d <some directory>`` This is where the configuration file will be stored
* ``-c <cert directory`` This is where certificates used by ``espm`` and enStratus will be written

By default, setup will write a configuration file to ``/opt/espm/etc``, create
certificates in ``/opt/espm/etc/certs`` and will listen on port 8443. These directories
must exist in advance:

.. code-block:: bash

	mkdir -p /opt/espm/etc/certs
	espm_setup

will result in:

::

	Preshared Key:
	b48hJWAW4Kicp74I-DCHdyYdrFfxijuaI2_CW0nb8HHxvA9Z

	Certificate:
	-----BEGIN CERTIFICATE-----
	MIIDeTCCAmECAgPoMA0GCSqGSIb3DQEBBQUAMIGBMQswCQYDVQQGEwJVUzESMBAG
	A1UECBMJTWlubmVzb3RhMRQwEgYDVQQHEwtNaW5uZWFwb2xpczEhMB8GA1UEChMY
	ZW5TdHJhdHVzIE5ldHdvcmtzLCBJbmMuMQ0wCwYDVQQLEwRlc3BtMRYwFAYDVQQD
	Ew1jZW50b3M1LXg4NjY0MB4XDTEyMDkwNjAzMTIyMloXDTIyMDkwNDAzMTIyMlow
	gYExCzAJBgNVBAYTAlVTMRIwEAYDVQQIEwlNaW5uZXNvdGExFDASBgNVBAcTC01p
	bm5lYXBvbGlzMSEwHwYDVQQKExhlblN0cmF0dXMgTmV0d29ya3MsIEluYy4xDTAL
	BgNVBAsTBGVzcG0xFjAUBgNVBAMTDWNlbnRvczUteDg2NjQwggEiMA0GCSqGSIb3
	DQEBAQUAA4IBDwAwggEKAoIBAQC1wP6evkhgycdHUSskbX7119HXL5xVSYLFpdq4
	2JV1p/2csMeWCoWQ4usWwe63AImKAW48HRlUut5IKXz/9vnIGm7/v71Zl5i4oWhl
	mR8icQSGjlxJrteJk6iGfeuwxxFwsOdePINti1yzsJw6K4xJm9OipyYOuEY/Nk7z
	83XA3WC4AmToVg7+EIhruRWbwrTgcnHqGSUZ479Nwb2NNb1FodxykG77PeHh79un
	p3RzIm4a04+mBUYFNsWBCCjNiPVN6Vew0vC2/1+aIWM6TzU5YYWT1mz5dPZUNdJe
	SEK8lNa4Yc5AXNY5fIx2/SLsYKOvOBq2KhCKR9QQiWDM3/iTAgMBAAEwDQYJKoZI
	hvcNAQEFBQADggEBAJLts1+LE7xkaTdo+IclJTg7kAza3RoviDw/LCJ4e1KDNWNW
	Zgs9CK8enpXYyD4dslKS87BO/T9Sh4qlgW2pu37YY7HM9WyECMdbDhqzD+mP2LlV
	BGf5q6K/D+raSY+/6Hkq9jpopw8q0giAgUic8ZM3L4l4YFG1KLTfY2Pr3nPhcX3B
	D/Y/PcJL15/nZj477s0SiwQSFIY5mS5JzqRe4RQcsRDafDFuhj7RCi2Yeplypxqm
	iXaeR8WZSTyE7QVBpfQOeMKgdGvascvmtFlkMbzShc+azV5JcChP0CK/yPQx5Dhu
	1TXCf+YarUm6s4MlC1eRxtWQwFmdRPIrh0vGXXg=
	-----END CERTIFICATE-----


	Please use these values in the appropriate form fields
	when adding your Puppet account to enStratus
	        
	Writing config to: /opt/espm/etc/espm.ini


Make note of the PSK and the certificate, you will need to provide these to enStratus. 

.. warning:: ``espm`` will refuse to overwrite any existing settings or certificates. The
   generated PSK and certificate are unique to each run of ``espm_setup``. If you change
   these or regenerate them, enStratus will no longer be able to communicate with the agent.
   You will have to delete and read the account in enStratus with the new values.

Starting up
~~~~~~~~~~~~
At this point you can simply run:

``espm -c /opt/espm/etc/espm.ini``

which starts ``espm`` in the foreground

::

	[06/Sep/2012:03:15:31] ENGINE Listening for SIGHUP.
	[06/Sep/2012:03:15:31] ENGINE Listening for SIGTERM.
	[06/Sep/2012:03:15:31] ENGINE Listening for SIGUSR1.
	[06/Sep/2012:03:15:31] ENGINE Bus STARTING
	[06/Sep/2012:03:15:31] ENGINE Started monitor thread '_TimeoutMonitor'.
	[06/Sep/2012:03:15:31] ENGINE Started monitor thread 'Autoreloader'.
	[06/Sep/2012:03:15:32] ENGINE Serving on 0.0.0.0:8443
	[06/Sep/2012:03:15:32] ENGINE Bus STARTED


We do not provide any sort of init script and logging is done to STDOUT. You are free to
wrap ``espm`` in the process monitor/init system of your choosing. We will be happy,
however, to work with you on getting it running with your init system.

Security
~~~~~~~~~

Every attempt has been made to ensure that ``espm`` does not contain any security flaws.
This is especially important since it has to run as root to interact with the PE2.5 rake
tasks and puppet commands.

However the only thing that needs to communicate with ``espm`` is enStratus. You are
welcome to firewall off access to ``espm`` except from the enStratus provisioning system.
We can provide you those IP addresses on request.

The PSK exists to authenticate enStratus to the agent. The certificate exists to ensure
that enStratus is talking to the correct agent.
