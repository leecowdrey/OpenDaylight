# OpenDaylighty Configuring the Controller

=============
Configuration

This chapter describes some basic configuration features for LSC.

Configuration Folder




Configuring LSC components includes the controller, extensions, UI, and apps.
Configuration is performed in the /opt/lumina/configuration directory.
The following figure displays a partial visual layout of the
configuration folder.







.. figure:: images/lsc-folders.jpg
:alt: Partial visual layout of the configuration folder




The following table describes the folders located in this folder:




.. list-table:: Configuration Folder
:widths: 20 60
:header-rows: 1




Folder
Description
Customer
Contains the configuration files to modify.
Factory
Contains the default configuration files. Do not modify the contents
of this folder.
Runtime
Contains the configuration files that result from the merger between
the customer files and factory files. Do not modify the contents of this folder.




Changes to the Configuration File
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^







Once you change a configuration file in the customer folder, you can apply
the change by either restarting the controller (recommended) or by running
the command lsc-ctl configure (on-the-fly change). In both cases, the
files are merges with the corresponding configuration file in the factory
folder to create a new configuration file. This file is stored in the runtime
folder. The process of merging configuration files uses a predictable algorithm
that overwrites any defaults with customer-defined overrides. This process
overrides specific controller settings, while still benefitting from changes
made to the default configuration file in the newer version. Overriding a
default value is simple since it only removes the specified override file.







LSC has two main services that are configurable: controller
(that is, lsc folder) and the UI (that is, ui folder).
Each folder has subfolders that have the services
where users can make and view configurations.




.. _configuring-the-controller:

Configuring the LSC and UI Services




Users can configure the LSC and UI services to suit specific requirements
by redefining the configuration files in the configuration/customer
folder. Perform the following steps to configure controller and UI services:







#. Edit the specific configuration file that resides under the
configuration/customer folder:
/opt/lumina/configuration/customer/<service>/<config_directory>







For example, to modify the lsc configuration file stored
under the /etc directory, create the file in the
/opt/lumina/configuration/customer/lsc/etc folder.







.. note:: Users can view existing configuration files that were merged
when a service was started in a runtime folder. For example,
view a merged value of a lsc configuration file stored
under the /etc directory in the following runtime
folder: /opt/lumina/configuration/customer/lsc/etc/




#. Edit the specific property.

.. _when-editing-config-files:




Editing Notes
^^^^^^^^^^^^^




Note the following when editing a configuration file:




The .properties and .cfg file types merge one line at a time
by using the key (that is, the value to the left of the equal
sign) as an identifier. If an identifier is redefined in a
configuration file within a configuration/customer folder, the
customer entry overwrites the default entry for that key only.
When editing a configuration in either the .cfg or .properties
files, it is recommended to include only the line that are changing
from the default value.







All other file types are not merged but replaced. When files are
replaced, the customer file must contain a complete copy of the
modified file with any added customer changes.




Restart a service by entering the following command:

.. code-block:: none




sudo systemctl restart lumina-ui
sudo systemctl restart lumina-lsc




Merged results appear in the runtime folder after running the configure action.




.. note:: Only run a configure action for changes that do not
require an LSC restart (i.e., modifying a log file).







Configuration is local to each node. Repeat the steps on each node to
enable the same functionality across a cluster.




Resetting to the Factory Defaults




Users can reset the configuration to the factory defaults for all LSC
controller and UI services. Perform the following steps to reset
services for the LSC or UI to their factory Defaults.







#. Remove the file or property that you want to change back to the
factory setting.







.. note:: Refer to the following notes :ref:when-editing-config-files
when editing a configuration file.




#. Restart the services by entering the following commands:

.. code-block:: none

  sudo systemctl restart lumina-lsc
  sudo systemctl restart lumina-ui





Configuration is local to each node. Repeat the steps on each node to
enable the same functionality across a cluster.




Configure Logging Files




Users can configure the logging files to suit specific requirements.
Perform the following to configure logging files:







#. Create (or edit) the following file:
/opt/lumina/configuration/customer/lsc/etc/org.ops4j.pax.logging.cfg







View current merged values of the org.ops4j.pax.logging.cfg
file in the runtime folder: /opt/lumina/configuration/ runtime/lsc/etc/org.ops4j.pax.logging.cfg




#. Edit the org.ops4j.pax.logging.cfg file and save the file. For example:

.. code-block:: none

  log4j2.appender.rolling.policies.size.size = 5MB





In this example, the log4j2.appender.rolling.policies.size.size property,
which specifies the maximum size of the logging file, is set to 5 MB.
For a list of all logging file properties available for configuration, open
the org.ops4j.pax.logging.cfg file in the runtime directory.




#. Enter the following command to configure the lumina-lsc service:

.. code-block:: none

  lsc-ctl configure


The maximum size of the logging file is 5 MB




Configuration is local to each node. Repeat the steps on each node to
enable the same functionality across a cluster.




HTTPS Configuration




HTTPS provides a bidirectional encryption to communicate between a client
and server. Encryption provides a reasonable guarantee that the contents
of the communication between the user and LSC cannot be read or forged by
a third party. By default, HTTPS is disabled for the LSC and UI.
A utility script is available to enable HTTPS. It performs the following:




Generates a CA certificates
Issues certificates for LTM by using the LSC CA
Enables HTTPS options in the appropriate configuration files

Enter the following command to see all available options for the HTTPS script:

.. code-block:: none




sudo lsc_secure_http -h
usage: lsc_secure_http [-h] [-i CONTROLLER_HOST] [-p CA_PASS_PHRASE] [-C]
{enable-https,disable-https}
Utility for easily enabling https on LSC and ui
positional arguments:
{enable-https,disable-https}
Action to take, either enable or disable HTTPS
optional arguments:
-h, --help show this help message and exit
-i CONTROLLER_HOST, --ip CONTROLLER_HOST
The IP address of the LSC host
-p CA_PASS_PHRASE, --pass-phrase CA_PASS_PHRASE
The pass phrase of the LSC CA key and keystore
-C, --controller-only







Enabling HTTPS
^^^^^^^^^^^^^^




Perform the following steps to enable HTTPS for LSC and LTM:

#. Run the following HTTPS utility script command:

.. code-block:: none

  sudo lsc_secure_http enable-https --ip <controller-ip> --pass-phrase <ca-pass-phrase>


The following example shows how to use the HTTPS utility:

.. code-block:: none

   [user@hostname]:~$ sudo lsc_secure_http enable-https
   Generating controller CA certificates...
   TLS directory already exists. Any existing keys will be overwritten
   creating signed cert
   Signature ok
   subject=/C=US/ST=CA/L=SF/O=ACME/OU=Node.js/CN=test/emailAddress=me@me.com <http://node.js/CN=test/emailAddress=me@me.com>
   Getting CA Private Key
   creating pkcs12 keystore
   JAVA_HOME not set. Using keytool from path.
   creating jks keystore
   Generating UI CA certificates, you will be prompted for the CA pass phrase...
   config path set to /opt/lumina/ui/web/config...
   Options provided: [ 'generate-cert' ]
   ca.crt file already exists, skipping generation...
   using caFile: /opt/lumina/ui/web/config/ca.crt
   using caKey: /opt/lumina/ui/web/config/ca.key
   Generating RSA private key, 2048 bit long modulus
   ...+++
   ...................+++
   e is 65537 (0x10001)
   Enter pass phrase for /opt/lumina/ui/web/config/ca.key:
   Signature ok
   subject=/C=US/ST=CA/L=San Jose/O=Grackle/CN=localhost
   Getting CA Private Key
   Certificate keypair generated.


#. Enter the following command to restart the LSC and LTM services:

.. code-block:: none

  sudo systemctl restart lumina-lsc
  sudo systemctl restart lumina-ui


HTTPS is enabled for the controller and UI.




Configuration is local to each node. Repeat the steps on each node to
enable the same functionality across a cluster.







Disable HTTPS
^^^^^^^^^^^^^




Perform the following steps to disable HTTPS for the LSC and UI:

#. Run the following HTTPS utility script command:

.. code-block:: none

  sudo lsc_secure_http disable-https


#. Restart the controller and LTM services by entering the following command:

.. code-block:: none

  sudo systemctl restart lumina-lsc
  sudo systemctl restart lumina-ui


HTTPS is disabled for the controller and UI.




Configuration is local to each node. Repeat the steps on each node to
enable the same functionality across a cluster.







HTTPS Utility Script Syntax
^^^^^^^^^^^^^^^^^^^^^^^^^^^




The following table describes the HTTPS utility (lsc_secure_http) script.




.. list-table:: HTTPS Utility Script Syntax
:widths: 20 50
:header-rows: 1




File
Description
lsc-secure-https enable-https
Enables HTTPS options in the LSC and UI configuration files.
lsc-secure-https disable-https
Disables HTTPS options in the LSC and UI configuration files.
ip controller-ip
Specifies the IP address of the LSC. If this keyword-and-argument
pair is not specified, the default is localhost.
pass-phrase ca-pass-phrase
Specifies the password phrase at the command line. If not specified,
users are prompted to enter the password. The default password is
adminadmin.
controller-only
Specifies that only controller certificates are to be generated and
only controller configuration files are to be modified.




Usage Guidelines
^^^^^^^^^^^^^^^^







Users must have sudo privileges to run the HTTPS utility script commands.
After enabling or disabling HTTPS, the LSC and UI services must be restarted
for the configuration changes to take effect.







External CA
^^^^^^^^^^^







It is possible to use an external CA certificate and key. The utility
looks for CA certificate and key in the ca-cert.pem and ca-key.pem
files, which are in the following directory:
/opt/lumina/configuration/customer/lsc/tls/







.. note:: Ensure that the external CA key is present before invoking the
utility. It is expected that a JKS keystore will still be present at
/opt/lumina/configuration/customer/lsc/tls/keystore.jks







HTTPs Examples
^^^^^^^^^^^^^^







The following example shows how to generate a CA certificate by using
the HTTPS utility:




.. code-block:: none

[user@hostname]:~$ sudo lsc_secure_http enable-https
Generating controller CA certificates...
TLS directory already exists. Any existing keys will be overwritten
creating signed cert
Signature ok
subject=/C=US/ST=CA/L=SF/O=ACME/OU=Node.js/CN=test/emailAddress=me@me.com <http://node.js/CN=test/emailAddress=me@me.com>
Getting CA Private Key
creating pkcs12 keystore
JAVA_HOME not set. Using keytool from path.
creating jks keystore
Generating UI CA certificates, you will be prompted for the CA pass phrase...
config path set to /opt/lumina/ui/web/config...
Options provided: [ 'generate-cert' ]
ca.crt file already exists, skipping generation...
using caFile: /opt/lumina/ui/web/config/ca.crt
using caKey: /opt/lumina/ui/web/config/ca.key
Generating RSA private key, 2048 bit long modulus
...+++
...................+++
e is 65537 (0x10001)
Enter pass phrase for /opt/lumina/ui/web/config/ca.key:
Signature ok
subject=/C=US/ST=CA/L=San Jose/O=Grackle/CN=localhost
Getting CA Private Key
Certificate keypair generated.


The following example shows how to disable HTTPS:

.. code-block:: none

[user@hostname]:~$ sudo lsc_secure_http disable-https

Memory Limits




The Java Virtual Machine (JVM) heap size determines how often and
how long the VM collects garbage. Garbage comprises objects that
cannot be reached from any pointer in a running program. A large
heap size may cause slower and infrequent garbage collection.
A heap size in accordance with memory requirements ensures
full garbage collection, which is faster and more frequent.







By default, the Java memory limit is 2 GB. To override the default
limit, export the JAVA_MAX_MEM environmental variable for the
lumina user. Only experienced users should perform these steps,
and only when the default memory allocation is not enough. Lumina
Networks recommends setting the memory limit to no more than 85
percent of the total system memory (up to a maximum of 8 GB).
Perform the following steps to configure the controller memory limits:







#. Create (or edit) the following file:
/opt/lumina/configuration/customer/lsc/etc/setenv.pre







View the current merged value of the setenv.pre file in
the runtime folder:
/opt/lumina/configuration/runtime/lsc/etc/setenv.pre




#. Add the following line to the setenv.pre file and save the file:

.. code-block:: none

   export JAVA_MAX_MEM=6G


#. Enter the following command to restart the LSC service:

.. code-block:: none

  sudo systemctl restart lumina-lsc


The maximum memory for the JVM is now set to 6 GB.




Configuration is local to each node. Repeat the steps on each node to
enable the same functionality across a cluster.




Garbage Collector Configuration




The garbage collector (GC) automatically manages dynamic memory allocation
requests for applications. A garbage collector executes automatic dynamic
memory management by performing the following:




Allocates from and gives back memory to the operating system.

Dispences the memory to the application as it is requested.

Determines the parts of the memory that is still in use by the application.

Reclaims the unused memory for reuse by the application.




The default Garbage Collector configuration is G1GC with maximum GC pause of 200 msecs.
Perform the following steps to change the default GC configuration:







#. Create (or edit) the following file:
/opt/lumina/configuration/customer/lsc/etc/setenv.pre







View the current merged value of the setenv.pre file in
the runtime folder:
/opt/lumina/configuration/runtime/lsc/etc/setenv.pre




#. Add the following line to the setenv.pre file and save the file:

.. code-block:: none

  export EXTRA_JAVA_OPTS="your GC configuration"


#. Enter the following command to restart the LSC services:

.. code-block:: none

  sudo systemctl restart lumina-lsc





Configuration is local to each node. Repeat the steps on each node to
enable the same functionality across a cluster.




JAVA_HOME Configuration




The JAVA_HOME environmental variable must be set to the correct
location for a valid controller environment. The path is not set by
default. Perform the following steps to configure JAVA_HOME:




#. Run the following command to check if JAVA_HOME is set:

.. code-block:: none

sudo systemctl status lumina-lsc


A warning may appear if JAVA_HOME is not set.




.. note:: JAVA_HOME may be set in the lumina user environment.
Any change here overrides that value.







#. Create (or edit) the following file:
/opt/lumina/configuration/customer/lsc/etc/setenv.pre







View the current merged values of the setenv.pre file in
the runtime folder:
/opt/lumina/configuration/runtime/lsc/etc/setenv.pre




#. Make the following modifications to the setenv.pre file and save the file:




.. note:: The following path is only an example. Your operating system may have
a different path.




.. code-block:: none

  export JAVA_HOME=<PATH_TO_JAVA_HOME>


#. Enter the following command to restart the LSC service:

.. code-block:: none

  sudo systemctl restart lumina-lsc


JAVA_HOME is now set.




Configuration is local to each node. Repeat the steps on each node to
enable the same functionality across a cluster.




Java Command Line Arguments




The KARAF_OPTS environmental variable can be used to add custom
command line arguments that affect the run-time configuration of the JVM
on which the controller runs. Perform the following steps to configure
the custom Java command line arguments:







#. Create (or edit) the following file:
/opt/lumina/configuration/customer/lsc/etc/setenv.pre







View the current merged values of the setenv.pre file in
the runtime folder:
/opt/lumina/configuration/runtime/lsc/etc/setenv.pre




#. Make the following modifications to the setenv.pre file and save the file:

.. code-block:: none

  export KARAF_OPTS=-DmyCustomArgument


#. Enter the following command to restart the LCS service:

.. code-block:: none

  sudo systemctl restart lumina-lsc





Configuration is local to each node. Repeat the steps on each node to
enable the same functionality across a cluster.
