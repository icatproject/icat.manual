| Component | Version | Installation                                                                                  | Documentation |
| --------- | ------- | ------------                                                                                  | ------------- |
| GlassFish | 4.0     | [Installation Guide (pdf)](https://javaee.github.io/glassfish/doc/4.0/installation-guide.pdf) | [ICAT Documentation](https://icatproject.org/installation/glassfish/) -  [Official Documentation](https://javaee.github.io/glassfish/documentation) |

Chapter 03: Installing GlassFish
===============================

Overview
--------
The GlassFish Application Server is installed by simply downloading a zip file and uncompressing it. The server is able to host multiple domains so we need to choose a domain name for our ICAT installation. In this tutorial, we will use the domain name `localhost`. The files for the domain are stored under `glassfish/<domain_name>`, in our case `glassfish/localhost`, under the top level GlassFish directory.

Download and unzip GlassFish
----------------------------
```Shell
wget http://download.java.net/glassfish/4.0/release/glassfish-4.0.zip
unzip glassfish-4.0.zip
```

Configure GlassFish
-------------------

Add the GlassFish directory to the path so we have access to the `asadmin` program which is used to administer the GlassFish application server. This step must be completed before running the script below as it uses the `asadmin` program.
```Shell
echo 'export PATH=$PATH:$HOME/glassfish4/bin' >> $HOME/.bashrc
source $HOME/.bashrc
```

Download a script to set up GlassFish for ICAT
```
wget https://icatproject.org/misc/scripts/setup-glassfish.py
```

Run the setup script. We pass it 3 options: the hostname, the maximum amount of memory to use and the password for the root account of the MySQL database. So for a domain name of `localhost`, with a maximum memory usage of 75% and a root MySQL account password of 'pw', run:
```Shell
python setup-glassfish.py localhost 75% pw
```

Configure GlassFish for MySQL
-----------------------------

We need the MySQL Connector library to enable ICAT to access the MySQL database. This was installed to the system in the previous chapter. We need to copy the jar file to the correct directory - the `localhost` domain of the GlassFish server - then restart GlassFish so that it is found.
```Shell
cp /usr/share/java/mysql-connector-java-5.1.17.jar $HOME/glassfish4/glassfish/domains/localhost/lib/ext/
asadmin stop-domain localhost
asadmin start-domain localhost
```

*N.B. Note the last 2 commands which restart GlassFish. It is often helpful to restart GlassFish when troubleshooting a problem with the software or installation process.*