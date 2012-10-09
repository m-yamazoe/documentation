.. _enstratus_public_images:

Current List of enStratus Public Images
---------------------------------------

Below is the list of currently available public images by cloud provider where appropriate. Not all cloud providers have public image sharing.

Public images can be searched for within enStratus under the `Compute/Machine Images` menu option.

Using a public image in automation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. note:: Due to a current limitation with enStratus, our public images are not IMMEDIATELY available for automation purposes. They must first be launched from the public image first. This behaviour will add the image to your private `Machine Images` list. Once this is done, you have the option of imaging the server WITHIN enStratus or contacting us to make it registered in your account.

If you would like us to add any of our public images to your account, please do the following:

* Launch one of the listed images below from within enStratus
* Once the instance is launched, you may terminate it. Make note of EBS vs. S3 instance store images in AWS.
* From the `Machine Images` list, open a support ticket with enStratus and provide the following information from the list:

  * Name
  * ID
  * Provider ID

Once this is support ticket is opened, we can immediately make the image available to you for automation. In the future, this will become a self-service operation. We apologize for any delays this causes you in getting started as quickly as possible.

AWS Public Images
``````````````````
All enStratus public images are owned by account `672283631203`. enStratus only provides x86_64/amd64 images.

+-----------------+--------------------------------------------------------------------------------+--------------+---------------+-----------------+
| Region          | Name                                                                           | AMI Id       | OS            | Storage type    |
+=================+================================================================================+==============+===============+=================+
| us-east-1       | enstratus-images/centos/enstratus17-CentOS58-x86_64-20121007.6                 | ami-4547fb2c | CentOS 5      | S3              |
+                 +--------------------------------------------------------------------------------+--------------+---------------+-----------------+
|                 | enstratus-images/ubuntu/enstratus17-Ubuntu1004-amd64-20121005.1                | ami-7f9a2616 | Ubuntu 10.04  | S3              |
+                 +--------------------------------------------------------------------------------+--------------+---------------+-----------------+
|                 | enstratus-images/ubuntu/enstratus17-Ubuntu1204-amd64-20120911.3                | ami-f319a89a | Ubuntu 12.04  | S3              |
+-----------------+--------------------------------------------------------------------------------+--------------+---------------+-----------------+
| us-west-1       | enstratus-images-us-west-1/centos/enstratus17-CentOS58-x86_64-20121008.1       | ami-a5fadde0 | CentOS 5      | S3              |
+                 +--------------------------------------------------------------------------------+--------------+---------------+-----------------+
|                 | enstratus-images-us-west-1/ubuntu/enstratus17-Ubuntu1004-amd64-20121005.1      | ami-25e9ce60 | Ubuntu 10.04  | S3              |
+                 +--------------------------------------------------------------------------------+--------------+---------------+-----------------+
|                 | enstratus-images-us-west-1/ubuntu/enstratus17-Ubuntu1204-amd64-20121005.1      | ami-19e9ce5c | Ubuntu 12.04  | S3              |
+-----------------+--------------------------------------------------------------------------------+--------------+---------------+-----------------+
| us-west-2       | enstratus-images-us-west-2/centos/enstratus17-CentOS58-x86_64-20121008.1       | ami-fc33bdcc | CentOS 5      | S3              |
+                 +--------------------------------------------------------------------------------+--------------+---------------+-----------------+
|                 | enstratus-images/ubuntu/enstratus17-ubuntu1004-amd64-20120915.1                | ami-b847c988 | Ubuntu 10.04  | S3              |
+                 +--------------------------------------------------------------------------------+--------------+---------------+-----------------+
|                 | enstratus-images/ubuntu/enstratus17-ubuntu1204-amd64-20120915.1                | ami-ba47c98a | Ubuntu 12.04  | S3              |
+-----------------+--------------------------------------------------------------------------------+--------------+---------------+-----------------+
| eu-west-1       | enstratus-images-eu/centos/enstratus17-CentOS58-x86_64-20121008.1              | ami-bf7272cb | CentOS 5      | S3              |
+                 +--------------------------------------------------------------------------------+--------------+---------------+-----------------+
|                 | enstratus-images-eu/ubuntu/enstratus17-Ubuntu1004-amd64-20121005.1             | ami-d15959a5 | Ubuntu 10.04  | S3              |
+                 +--------------------------------------------------------------------------------+--------------+---------------+-----------------+
|                 | enstratus-images-eu/ubuntu/enstratus17-Ubuntu1204-amd64-20121005.2             | ami-0d595979 | Ubuntu 12.04  | S3              |
+-----------------+--------------------------------------------------------------------------------+--------------+---------------+-----------------+
| ap-northeast-1  | enstratus-images-ap-northeast-1/ubuntu/enstratus17-Ubuntu1004-amd64-20121005.1 | ami-40823d41 | Ubuntu 10.04  | S3              |
+                 +--------------------------------------------------------------------------------+--------------+---------------+-----------------+
|                 | enstratus-images-ap-northeast-1/ubuntu/enstratus17-Ubuntu1204-amd64-20121005.1 | ami-3e823d3f | Ubuntu 12.04  | S3              |
+-----------------+--------------------------------------------------------------------------------+--------------+---------------+-----------------+
| ap-southeast-1  | enstratus-images-ap-southeast-1/centos/enstratus17-CentOS58-x86_64-20121008.1  | ami-76a8e824 | CentOS 5      | S3              |
+                 +--------------------------------------------------------------------------------+--------------+---------------+-----------------+
|                 | enstratus-images-ap-southeast-1/ubuntu/enstratus17-Ubuntu1004-amd64-20121008.1 | ami-4ea8e81c | Ubuntu 10.04  | S3              |
+                 +--------------------------------------------------------------------------------+--------------+---------------+-----------------+
|                 | enstratus-images-ap-southeast-1/ubuntu/enstratus17-Ubuntu1204-amd64-20121008.1 | ami-4ca8e81e | Ubuntu 12.04  | S3              |
+-----------------+--------------------------------------------------------------------------------+--------------+---------------+-----------------+

