Cloud Credentials
-----------------
When the user enters cloud credentials into the enStratus console, those
credentials are passed to the dispatcher via a webservices call. The
console authenticates this call to the dispatcher using the encryption
keys generated as part of the installation process. The communication
between the console and the dispatcher is encrypted using SSL. The cloud
credentials are never at rest on the server upon which the dispatcher
process runs, instead, they are passed to the KM (key management) service via a
webservices call. The dispatcher authenticates this call via encryption
keys generated as part of the installation process. The communication
between the console and the dispatcher is encrypted using SSL. Finally,
the keys are stored in the credentials database as follows:

All customer encryption and authentication credentials are stored in an
AES256-encrypted database. No encryption keys exist in the credentials
management zone. Each row in the credentials database is encrypted and
decrypted on the provisioning server, using an encryption key unique to
each customer account. No global key exists and no key is ever stored on
any file system within our infrastructure.

Using Those Credentials
-----------------------
When a user attempts to provision a resource that requires the use of
the cloud credentials, the following process takes place.

The user clicks launch or makes an api call to launch a cloud server.
The dispatcher fields this request from the console or api service and
passes that request to the dispatcher via a webservices call. The appropriate
key is decrypted out of the KM database (in memory only), then it is encrypted
using the agent's session key and passed to the agent over it's SSL session.
Once the credential has been succesfully passed to the agent, the dispatcher
securely deletes it from memory. 

The agent then decrypts the credential (again only in memory), uses the 
credential as necessary and then securely deletes the credentials from 
memory. 

With one exception, enStratus never writes the credentials to disk in clear 
text. In the case of AWS and similar services that require credentails on disk
to create images (such as non-EBS root instances) the credentials are written
to disk just long enough for the image to be created.

Credentials enStratus Supports
------------------------------

In addition to supporting traditional passwords and encryption keys, enStratus
is also application aware. As such it can securely store configuration files
for applications such as mysql, tomcat and apache that can have clear-text
credentials stored in them. The process is that same as described above
but intead of passing a password or API key, enStratus decrypts, encrypts etc 
the entire configuration file, which is then uses to start the appropriate 
service. This is also how enStratus handles credentials for disk encryption
or backup encryption.

