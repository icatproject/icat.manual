| Component   | Version | Installation                                                                                | Documentation |
| ---------   | ------- | ------------                                                                                | ------------- |
| icat.server | 4.8.0   | [Installation Guide](https://repo.icatproject.org/site/icat/server/4.8.0/installation.html) | [Component Documentation](https://icatproject.org/user-documentation/icat-server/) |

Chapter 05: Installing the ICAT Server
=====================================

Overview
--------
The ICAT Server is the central component of an ICAT installation. It manages authenticating users via its plugins, it provides REST and SOAP interfaces to the data catalogue, it uses Lucene to make its data searchable and it coordinates the ICAT Data Service which allows users to access their data. The installation process is the same as before: download, unzip, edit configuration files, then run `./setup configure` and `./setup install`. The main configuration file - `icat.properties` - is quite large. Most values can be safely left to their defaults and the values which need changing will be indicated below.

Download and unzip
------------------
From your home directory, run:
```Shell
wget https://repo.icatproject.org/repo/org/icatproject/icat.server/4.8.0/icat.server-4.8.0-distro.zip
unzip icat.server-4.8.0-distro.zip
```

Configure the ICAT Server
-------------------------

Change directory to the `icat.server` directory and open the `icat-setup.properties` file. It has 2 sections. The first will be familiar from the authentication plugin in Chapter 4 where we set `secure` to `false` and configured the `container`, its location and port. The second section tells ICAT how to access the MySQL database. The `db.driver` parameter specifies the location of the database access code - in the jar file which we copied earlier. The `db.url` parameter tells ICAT where to contact the database. In this case, it uses the jdbc-mysql connector to access the local machine on port 3306 (the default for MySQL) and uses the `icat` database. Finally, we specify the database username and password for ICAT to use. We created these in Chapter 2.
```INI
#Glassfish
secure = false
container = Glassfish
home = /home/glassfish/glassfish4
port = 4848

# MySQL
db.driver      = com.mysql.jdbc.jdbc2.optional.MysqlDataSource
db.url         = jdbc:mysql://localhost:3306/icat
db.username    = icat
db.password    = icat
```

ICAT uses Lucene to create an index of its data so that users can search for what they need. We need to create a directory for this index.
```Shell
mkdir -p /home/glassfish/icat/data/lucene
```

The ICAT install script installs a few executable scripts. We need to create a directory for them and add it to our `$PATH` variable.
```Shell
mkdir -p /home/glassfish/bin
echo 'export PATH=$PATH:/home/glassfish/bin' >> ~/.bashrc
source ~/.bashrc
```

The `icat.properties` file is quite large but we only need to change a few of the parameters: to specify the privileged `root` user, the authentication methods we are using and the location of the Lucene data directory that we created above. Set the `rootUserNames` parameter to `simple/root` - that is the `root` username configured using the `simple` authentication mechanism. Set `authn.list` to `simple` - we are only using the simple authentication plugin in this tutorial. Uncomment the `authn.simple.jndi` and `authn.simple.friendly` and leave them unchanged. These tell ICAT where to find the `simple` authentication plugin and what to call it. Finally, set the `lucene.directory` parameter to the directory created above: `/home/glassfish/icat/data/lucene`.
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
!authn.list = db ldap simple anon
authn.list = simple

# Parameters for each of the four plugins
!authn.db.jndi       = java:global/authn.db-1.2.0/DB_Authenticator

!authn.ldap.jndi     = java:global/authn.ldap-1.2.0/LDAP_Authenticator
!authn.ldap.admin    = true
!authn.ldap.friendly = Federal Id

authn.simple.jndi = java:global/authn.simple-1.1.0/SIMPLE_Authenticator
authn.simple.friendly = Simple

!authn.anon.jndi     = java:global/authn.anon-1.1.1/ANON_Authenticator
!authn.anon.friendly = Anonymous

# Uncomment to permit configuration of logback
!logback.xml = icat.logback.xml

# Notification setup
notification.list = Dataset Datafile
notification.Dataset = CU
notification.Datafile = CU

# Call logging setup
log.list = SESSION WRITE READ INFO

# Lucene
lucene.directory = /home/glassfish/icat/data/lucene
lucene.commitSeconds = 5
lucene.commitCount = 10000

# JMS - uncomment and edit if needed
!jms.topicConnectionFactory = java:comp/DefaultJMSConnectionFactory

# Optional key which must match that of the IDS server if the IDS is in use and has a key for digest protection of Datafile.location
!key = ???
```

Install the ICAT Server
-----------------------
As before:
```Shell
./setup -vv install
```

Test the ICAT Server
--------------------

run testicat?
