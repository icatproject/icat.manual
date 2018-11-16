| Component   | Version | Installation                                                                                | Documentation |
| ---------   | ------- | ------------                                                                                | ------------- |
| ids.server  | 1.9.0   | [Installation Guide](https://repo.icatproject.org/site/ids/server/1.9.0/installation.html)  | [Component Documentation](https://icatproject.org/user-documentation/icat-data-service/) |

Chapter 08: Install the ICAT Data Service (IDS)
==============================================

Overview
--------

As stated in the previous chapter, the ICAT Data Service (IDS) controls access to the underlying data files which the ICAT Server catalogues and indexes. To configure the IDS, we tell it about the privileged user accounts, the storage plugin, the ICAT Server, and where to put its files.

Install the IDS
---------------
```Shell
cd ~/downloads
curl -O 'https://repo.icatproject.org/repo/org/icatproject/ids.server/1.9.0/ids.server-1.9.0-distro.zip'
cd ~/install
unzip ~/downloads/ids.server-1.9.0-distro.zip
```

Configure the IDS
-----------------
First, we need to make directories to store the data files - one for `main` storage and one for `archive` storage. This simulates the storage systems which the IDS would connect to as the sources of its data.
```Shell
mkdir -p ~/data/main
mkdir -p ~/data/archive
```

We create a cache directory for the IDS to store its data. This is the destination for the data which the IDS has retrieved.
```Shell
mkdir -p ~/data/cache
```

Change directory into `~/install/ids.server`. The `setup.properties` should be familiar from previous component installations. There is one addtional parameter: we set `libraries` to the name of the storage plugin jar file which we installed in the previous chapter. In the example below, there is a `*` which allows the jar file to be found regardless of version.

```INI
#Glassfish
secure = true
container = Glassfish
home = /home/glassfish/payara41
port = 4848

#Any libraries needed (space separated list of jars in domain's lib/applibs
libraries=ids.storage_file*.jar
```

The `run.properties` file is quite large but most of the parameters can safely be left as their default values. For this tutorial, we only need to tell the IDS where to find the ICAT Server, who the privileged users are, and where to find and store its data.
* Set `icat.url` to the location of the ICAT Server: `https://localhost:8181`.
* Set the `plugin.main.dir` parameter to the point to the directory we created above: `/home/glassfish/data/main`.
* Set the `cache.dir` parameter to the point to the directory we created above: `/home/glassfish/data/cache`.
* Set the `rootUserNames` parameter to `simple/root` - the `root` user configured using the `simple` authentication mechanism. Users listed here are permitted to check the status of the whole IDS. More accounts could be listed here as a space-separated list.
* Set the `reader` parameter to `simple username root password rootpw`. The format for this parameter is '*mechanism* `username` *username_of_account* `password` *password_of_account*' - so set it to the `reader` account we created in [chapter 4](04InstallingAuthentication.md). The user specified here is permitted to read all data in the IDS.
* Set the `plugin.archive.dir` parameter to the point to the directory we created above: `/home/glassfish/data/archive`.
* Finally, configure file checking. When a file is added to the IDS, its checksum is stored in the ICAT database. The IDS periodically scans files to check that their checksum matches what it expects. The location of two files needs to be configured: `filesCheck.lastIdFile` - which contains the ID of the last file to be checked (this enables the IDS to continue checking files where it left off if it is restarted); and `filesCheck.errorLog` - which contains a list of files with errors.

```INI
# General properties
icat.url = https://localhost.localdomain:8181

plugin.zipMapper.class = org.icatproject.ids.storage.ZipMapper

plugin.main.class = org.icatproject.ids.storage.MainFileStorage
plugin.main.dir = /home/glassfish/data/main/

cache.dir = /home/glassfish/data/cache
preparedCount = 10000
processQueueIntervalSeconds = 5
rootUserNames = simple/root
sizeCheckIntervalSeconds = 60
reader = simple username reader password readerpw
!readOnly = true
maxIdsInQuery = 1000

# Properties for archive storage
plugin.archive.class = org.icatproject.ids.storage.ArchiveFileStorage
plugin.archive.dir = /home/glassfish/data/archive/
writeDelaySeconds = 60
startArchivingLevel1024bytes = 5000000
stopArchivingLevel1024bytes =  4000000
storageUnit = dataset
tidyBlockSize = 500

# File checking properties
filesCheck.parallelCount = 5
filesCheck.gapSeconds = 5
filesCheck.lastIdFile = /home/glassfish/logs/lastIdFile
filesCheck.errorLog = /home/glassfish/logs/filesCheckErrors.log

# Link properties
linkLifetimeSeconds = 3600

# JMS Logging
log.list = READ WRITE INFO LINK MIGRATE PREPARE

# JMS - uncomment and edit if needed
!jms.topicConnectionFactory = java:comp/DefaultJMSConnectionFactory
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

Also note in this file that the log file location is set to `${HOME}/logs/ids.log`.

Install the IDS
---------------
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
ids.server-1.9.0    <ejb, webservices, web>
Command list-applications executed successfully.
```

Test the IDS
------------

We can check that the IDS is running by visiting this URL from inside the VM:

```Shell
curl -k https://localhost:8181/ids/ping
```
and this URL from your browser:

(https://localhost:18181/ids/ping)

Both should output the text `IdsOK`.
