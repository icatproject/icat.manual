| Component   | Version | Installation                                                                                | Documentation |
| ---------   | ------- | ------------                                                                                | ------------- |
| icat.server | 4.9.1   | [Installation Guide](https://repo.icatproject.org/site/icat/server/4.9.1/installation.html) | [Component Documentation](https://icatproject.org/user-documentation/icat-server/) |

Chapter 06: Installing the ICAT Server
=====================================

Overview
--------
The ICAT Server is the central component of an ICAT installation. It manages authenticating users via its plugins, it provides REST and SOAP interfaces to the data catalogue, it uses Lucene to make its data searchable and it provides metadata to the ICAT Data Service which allows users to download their files. The installation process is the same as before: download, unzip, edit configuration files, then run `./setup install`. The main configuration file - `run.properties` - is quite large. Most values can be safely left to their defaults and the values which need changing will be indicated below.

**NB: the commands on this page should be entered as the `glassfish` user**

Download and unzip
------------------
From your home directory, run:
```Shell
cd ~/downloads/
curl -O 'https://repo.icatproject.org/repo/org/icatproject/icat.server/4.9.1/icat.server-4.9.1-distro.zip'
cd ~/install
unzip ~/downloads/icat.server-4.9.1-distro.zip
```

Configure the ICAT Server
-------------------------

Change directory to the `icat.server` directory and open the `setup.properties` file. It has 2 sections. The first will be familiar from the previous chapters. The second section tells ICAT how to access the MariaDB database. The `db.driver` parameter specifies the location of the database access code - in the jar file which we copied earlier. The `db.url` parameter tells ICAT where to contact the database. In this case, it uses the jdbc-mysql connector to access the local machine on port 3306 (the default for MySQL/MariaDB) and uses the `icatdb` database. Finally, we specify the database username and password for ICAT to use. We created these in Chapter 2.
```INI
#Glassfish
secure = true
container = Glassfish
home = /home/glassfish/payara41
port = 4848

# MySQL
db.driver      = com.mysql.jdbc.jdbc2.optional.MysqlDataSource
db.url         = jdbc:mysql://localhost:3306/icatdb
db.username    = icatdbuser
db.password    = icatdbuserpw
```

The ICAT install script installs a few executable scripts. We need to create a directory for them and add it to our `$PATH` variable.
```Shell
mkdir -p /home/glassfish/bin
echo 'export PATH=$PATH:/home/glassfish/bin' >> ~/.bashrc
source ~/.bashrc
```

There are a lot of parameters for the ICAT Server but we only need to change a few. Copy the `run.properties.example` file to `run.properties` and open it in an editor.
* Set the `rootUserNames` parameter to `simple/root` - that is the `root` username configured using the `simple` authentication mechanism.
* Set `authn.list` to `simple` - we are only using the simple authentication plugin in this tutorial.
* Add `authn.simple.friendly = Simple` below the `authn.simple.url`. These tell ICAT where to find the `simple` authentication plugin and what to call it.
* Finally, set the `lucene.directory` parameter to the directory created before: `/home/glassfish/data/lucene`.

```INI
# Real comments in this file are marked with '#' whereas commented out lines
# are marked with '!'

# The lifetime of a session
lifetimeMinutes = 120

# Provide CRUD access to authz tables
rootUserNames = simple/root

# Restrict total number of entities to return in a search call
maxEntities = 10000

# Maximum ids in a list - this must not exceed 1000 for Oracle
maxIdsInQuery = 500

# Size of cache to be used when importing data into ICAT
importCacheSize = 50

# Size of cache to be used when exporting data from ICAT
exportCacheSize = 50

# Desired authentication plugin mnemonics
authn.list = simple

# Parameters for each of the four plugins
!authn.db.jndi       = java:global/authn.db-1.2.0/DB_Authenticator

!authn.ldap.jndi     = java:global/authn.ldap-1.2.0/LDAP_Authenticator
!authn.ldap.admin    = true
!authn.ldap.friendly = Federal Id

authn.simple.url      = https://localhost:8181
authn.simple.friendly = Simple

!authn.anon.url      = https://localhost:8181
!authn.anon.friendly = Anonymous

# Notification setup
notification.list = Dataset Datafile
notification.Dataset = CU
notification.Datafile = CU

# Call logging setup
log.list = SESSION WRITE READ INFO

# Lucene
lucene.url = https://localhost.localdomain:8181
lucene.populateBlockSize = 1000
lucene.directory = /home/glassfish/data/lucene
lucene.backlogHandlerIntervalSeconds = 60
lucene.enqueuedRequestIntervalSeconds = 5

# List members of cluster
!cluster = http://vm200.nubes.stfc.ac.uk:8080 https://smfisher:8181

# Optional key which must match that of the IDS server if the IDS is in use and has a key for digest protection of Datafile.location
!key = ???
```

Configure verbose logging
-------------------------

Configure **DEBUG** level logging to aid in troubleshooting.


```Shell
cp logback.xml.example logback.xml
```

Change

```XML
        <root level="INFO">
                <appender-ref ref="FILE" />
        </root>
```
to
```XML
        <root level="DEBUG">
                <appender-ref ref="FILE" />
        </root>
```

Also note in this file that the log file location is set to `${HOME}/logs/icat.log`.

Install the ICAT Server
-----------------------
As before:
```Shell
./setup -vv install
```

Check that it is running
------------------------

```Shell
asadmin list-applications
```
should output:
```
authn.simple-2.0.0  <ejb, web>
icat.server-4.9.1   <ejb, webservices, web>
icat.lucene-1.1.0   <ejb, web>
Command list-applications executed successfully.
```

Test the ICAT Server
--------------------

The ICAT Server installation comes with a script for testing which is installed in `~/bin`. Run the script giving it the URL for ICAT, the authentication mnemonic, username and password like this:

```Shell
testicat <url> <mnemonic> username <username> password <password>
```

So for this tutorial, the values would be:
```Shell
testicat https://localhost:8181 simple username root password pw
```
and the output would look like this (ignoring a warning about certificates):
```
ICAT version 4.9.1
Logged in as simple/root with 119.996166667 minutes to go
Login, search, create, delete and logout operations were all successful.
```

Check access from your browser
------------------------------

You can check that the ICAT Server is running in your browser. If you have not done so already, you may have to click through to accept the connection to an untrusted site. With port `18181` on your host machine mapped to port `8181` on the VM:

(https://localhost:18181/icat/version)

will return:
```JSON
{"version":"4.9.1"}
```
