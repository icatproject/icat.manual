| Component | Version | Installation                                                                                  | Documentation |
| --------- | ------- | ------------                                                                                  | ------------- |
| Payara | 4.1.2.181     | [Installation Guide (pdf)](https://docs.payara.fish/v/181/getting-started/getting-started.html) | [ICAT Documentation](https://icatproject.org/installation/glassfish/) -  [Official Documentation](https://docs.payara.fish/v/181/) |

Chapter 03: Installing Payara
=============================

Overview
--------
The Payara Application Server is installed by simply downloading a zip file and uncompressing it. The server is able to host multiple domains so we need to choose a domain name for our ICAT installation. In this tutorial, we will use the default domain name `domain1`. The files for the domain are stored under `payara41/glassfish/<domain_name>`, in our case `payara41/glassfish/domain1`.

**NB: the commands on this page should be entered as the `glassfish` user**

Create Directories for the Files
--------------------------------

```Shell
mkdir downloads install scripts
```

Download and unzip Payara
-------------------------

```Shell
cd downloads/
curl -O 'http://search.maven.org/remotecontent?filepath=fish/payara/distributions/payara/4.1.2.181/payara-4.1.2.181.zip'
cd ~
unzip downloads/payara-4.1.2.181.zip
```

Configure Payara
----------------

Add the Payara directory to the path so we have access to the `asadmin` program which is used to administer the Payara application server. This step must be completed before running the script below as it uses the `asadmin` program.

```Shell
echo 'export PATH=$HOME/payara41/bin:$PATH' >> $HOME/.bashrc
source $HOME/.bashrc
```
Check that it works:

```Shell
which asadmin
```
outputs: *~/payara41/bin/asadmin*

Download a script to configure Payara for ICAT

```Shell
cd scripts/
curl -O https://icatproject.org/misc/scripts/setup-glassfish.py
cd
```

Run the setup script. We pass it 3 options: the domain name, the maximum amount of memory to use and the password for glassfish `asadmin` utility. So for a domain name of `domain1`, with a maximum memory usage of 75% and a glassfish `asadmin` password of '$ASADMIN_PASSWD', run:

```Shell
python scripts/setup-glassfish.py domain1 75% $ASADMIN_PASSWD
```

Configure Payara for MariaDB
----------------------------

We need the MySQL Connector library to enable ICAT to access the MariaDB database. This was installed to the system in the previous chapter. We need to copy the jar file to the correct directory - the `domain1` domain of the Payara server - then restart Payara so that it is found.

```Shell
cp /usr/share/java/mysql-connector-java-5.1.17.jar $HOME/payara41/glassfish/domains/domain1/lib/ext/
asadmin stop-domain
asadmin start-domain
```

*N.B. Note the last 2 commands which restart Payara. It is often helpful to restart Payara when troubleshooting a problem with the software or installation process.*

Check the certificate
---------------------

In this tutorial, we will set up the components to communicate with each other securely. To do this, the hostname we give for a component must match the Common Name (CN) in the certificate provided by that component. Since all the components run inside Payara, they provide Payara's certificate. By default, Payara uses a self-signed certificate. It is beyond the scope of this tutorial to cover how to install certificates in Payara so we will use the self-signed certificate. You can find more information on installing certificates [here](https://icatproject.org/installation/glassfish/certificate/).

To find the hostname of your VM:
```Shell
hostname
```
will output `localhost.localdomain` if you are using the Vagrant setup recommended in this tutorial.

To find the Common Name (CN) of the certificate provided by Payara:
```Shell
echo | openssl s_client -connect localhost:8181  -showcerts 2> /dev/null | egrep 'subject|issuer'
```
will output:
```
subject=/C=UK/ST=Worcestershire/L=Great Malvern/O=Payara Foundation/OU=Payara/CN=localhost.localdomain
issuer=/C=UK/ST=Worcestershire/L=Great Malvern/O=Payara Foundation/OU=Payara/CN=localhost.localdomain
```
if you have followed the Vagrant setup.

Check access from your browser
------------------------------

You can check that Payara is accessible from your browser by loading the admin page. There is no need to log in - just check that the page loads. You will have to click through to accept the connection to an untrusted site as the self-signed certificate is not trusted by your browser.

If you have followed the recommended Vagrant set up, then port `14848` on your host machine will be  mapped to port `4848` on the VM.

(https://localhost:14848)

Troubleshooting: Finding the Logs
---------------------------------

If you need to troubleshoot a problem, you can find the Payara logs at:

```Shell
/home/glassfish/payara41/glassfish/domains/domain1/logs/server.log
```

