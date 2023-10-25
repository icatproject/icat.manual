| Component | Version | Installation                                                                                  | Documentation |
| --------- | ------- | ------------                                                                                  | ------------- |
| Payara | 5.2022.1     | [Installation Guide](https://docs.payara.fish/community/docs/5.2022.1/getting-started/getting-started.html) | [ICAT Documentation](https://icatproject.org/installation/glassfish/) -  [Official Documentation](https://docs.payara.fish/community/docs/5.2022.1/README.html) |
| Payara    | 6.2023.8 | [Installation Guide](https://docs.payara.fish/community/docs/6.2023.8/General%20Info/Getting%20Started.html) | [ICAT Documentation](https://icatproject.org/installation/glassfish/) -  [Official Documentation](https://docs.payara.fish/community/docs/6.2023.8/Overview.html) |

Chapter 03: Installing Payara
=============================

Overview
--------
The Payara Application Server is installed by simply downloading a zip file and uncompressing it. The server is able to host multiple domains so we need to choose a domain name for our ICAT installation. In this tutorial, we will use the default domain name `domain1`. The files for the domain are stored under `payara6/glassfish/domains/<domain_name>`, in our case `payara6/glassfish/domains/domain1`.

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
curl -O 'https://repo1.maven.org/maven2/fish/payara/distributions/payara/6.2023.8/payara-6.2023.8.zip'
cd ~
unzip downloads/payara-6.2023.8.zip
```

Configure Payara
----------------

Add the Payara directory to the path so we have access to the `asadmin` program which is used to administer the Payara application server. This step must be completed before running the script below as it uses the `asadmin` program.

```Shell
echo 'export PATH=$HOME/payara6/bin:$PATH' >> $HOME/.bashrc
source $HOME/.bashrc
```
Check that it works:

```Shell
which asadmin
```
outputs: `~/payara6/bin/asadmin`

Download a script to configure Payara for ICAT

```Shell
cd scripts/
curl -O https://raw.githubusercontent.com/icatproject/icat.utils/master/src/main/scripts/setup-glassfish.py
cd
```

Run the setup script. We pass it 3 options: the domain name, the maximum amount of memory to use and the password for the root account of the MariaDB database. So for a domain name of `domain1`, with a maximum memory usage of 75% and a root MariaDB account password of 'pw', run:

```Shell
python scripts/setup-glassfish.py domain1 75% pw
```

If using a machine which previously had Java 8 installed, then you may get errors for `/usr/lib/jvm/java-11-openjdk-11.0.20.0.8-2.el8.x86_64/lib/tzdb.dat` being missing. This can occur when there is a symlink pointing to a file location previously associated with Java 8 such as `/usr/share/javazi-1.8/tzdb.dat`. In this, case, the problem can be resolved by removing the symlink, re-installing Java 8, and copying the timezones file to the Java 11 location.

Configure Payara for MariaDB
----------------------------

We need the MySQL Connector library to enable ICAT to access the MariaDB database. This was installed to the system in the previous chapter. We need to copy the jar file to the correct directory - the `domain1` domain of the Payara server - then restart Payara so that it is found.

TODO do I need this?
```Shell
cp /usr/share/java/mysql-connector-java.jar $HOME/payara6/glassfish/domains/domain1/lib/
asadmin stop-domain
asadmin start-domain
```

*N.B. Note the last 2 commands which restart Payara. It is often helpful to restart Payara when troubleshooting a problem with the software or installation process.*

Check the certificate
---------------------

In this tutorial, we will set up the components to communicate with each other securely. To do this, the hostname we give for a component must match the Common Name (CN) in the certificate provided by that component. Since all the components run inside Payara, they provide Payara's certificate. By default, Payara uses a self-signed certificate. It is beyond the scope of this tutorial to cover how to install certificates in Payara so we will use the self-signed certificate. You can find more information on installing certificates [here](https://icatproject.org/installation/glassfish-certificate/).

To find the hostname of your VM:
```Shell
hostname
```
will output `localhost` if you are using the Vagrant setup recommended in this tutorial. If you are using a Vagrant VM and the hostname is set to `localhost.localdomain`, please follow the instructions in chapter 1 (Vagrant setup) to change the hostname to `localhost`. Once you have applied the change, re-run the `setup-glassfish.py` script as shown above.

To find the Common Name (CN) of the certificate provided by Payara:
```Shell
echo | openssl s_client -connect localhost:8181  -showcerts 2> /dev/null | egrep 'subject|issuer'
```
will output:
```
subject=/C=UK/ST=Worcestershire/L=Great Malvern/O=Payara Foundation/OU=Payara/CN=localhost
issuer=/C=UK/ST=Worcestershire/L=Great Malvern/O=Payara Foundation/OU=Payara/CN=localhost
```
if you have followed the Vagrant setup.

If not using Vagrant, the `CN` may not be `localhost`. In this case, it will be necessary to replace `localhost` with whatever appears in your certificate in the subsequent `run.properties` files to ensure that https communication succeeds between the different ICAT components.

Check access from your browser
------------------------------

You can check that Payara is accessible from your browser by loading the admin page. There is no need to log in - just check that the page loads. You will have to click through to accept the connection to an untrusted site as the self-signed certificate is not trusted by your browser.

If you have followed the recommended Vagrant set up, then port `14848` on your host machine will be  mapped to port `4848` on the VM.

(https://localhost:14848)

Troubleshooting: Finding the Logs
---------------------------------

If you need to troubleshoot a problem, you can find the Payara logs at:

```Shell
/home/glassfish/payara5/glassfish/domains/domain1/logs/server.log
```

