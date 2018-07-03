| Component   | Version | Installation                                                                                | Documentation |
| ---------   | ------- | ------------                                                                                | ------------- |
| ids.server  | 1.7.0   | [Installation Guide](https://repo.icatproject.org/site/ids/server/1.7.0/installation.html)  | [Component Documentation](https://icatproject.org/user-documentation/icat-data-service/) |

Chapter 07: Install the ICAT Data Service (IDS)
==============================================

Overview
--------

As stated in the previous chapter, the ICAT Data Service (IDS) controls access to the underlying data files which the ICAT Server catalogues and indexes. To configure the IDS, we inform it where to find the components that we set up in previous chapters: the privileged user accounts, the storage plugin and the ICAT Server and where to put its files.

Install the IDS
---------------
```Shell
wget https://repo.icatproject.org/repo/org/icatproject/ids.server/1.7.0/ids.server-1.7.0-distro.zip
unzip ids.server-1.7.0-distro.zip
```

Configure the IDS
-----------------

The `ids-setup.properties` should be familiar from previous component installations. There is one addtional parameter: we set `libraries` to the name of the storage plugin jar file which we installed in the previous chapter. In the example below, there is a `*` which allows the jar file to be found regardless of version.

```INI
#Glassfish
secure = false
container = Glassfish
home = /home/glassfish/glassfish4
port = 4848

#Any libraries needed (space separated list of jars in domain's lib/applibs
libraries=ids.storage_file*.jar
```

We create a cache directory for the IDS to store its data.
```Shell
mkdir -p /home/glassfish/icat/main/ids/cache
```

The `ids.properties` file is quite large but most of the parameters can safely be left as their default values. For this tutorial, we only need to tell the IDS where to find the ICAT Server, who the privileged users are and where to store its data. 
* Set `icat.url` to the location of the ICAT Server: `http://localhost:8080`. In our case, since we are not creating a secure installation, `http` is fine.
* Set the `cache.dir` parameter to the point to the directory we created above: `/home/glassfish/icat/main/ids/cache`. 
* Set the `rootUserNames` parameter to `simple/root` - the `root` user configured using the `simple` authentication mechanism. Users listed here are permitted to check the status of the whole IDS. More accounts could be listed here as a space-separated list. 
* Set the `reader` parameter to `simple username root password pw`. The format for this parameter is '*mechanism* `username` *username_of_account* `password` *password_of_account*' - so set it to the same privileged `root` account as before. The user specified here is permitted to read all data in the IDS.
* Finally, configure file checking. When a file is added to the IDS, its checksum is stored in the ICAT database. The IDS periodically scans files to check that their checksum matches what it expects. The location of two files needs to be configured: `filesCheck.lastIdFile` - which contains the ID of the last file to be checked (this enables the IDS to continue checking files where it left off if it is restarted); and `filesCheck.errorLog` - which contains a list of files with errors.

```INI
# General properties
icat.url = http://localhost:8080

plugin.zipMapper.class = org.icatproject.ids.storage.ZipMapper

plugin.main.class = org.icatproject.ids.storage.MainFileStorage
plugin.main.properties = ids.storage_file.main.properties

cache.dir = /home/glassfish/icat/main/ids/cache
preparedCount = 10000
processQueueIntervalSeconds = 5
rootUserNames = simple/root
sizeCheckIntervalSeconds = 60
reader = simple username root password pw
!readOnly = true
maxIdsInQuery = 1000

# Properties for archive storage
plugin.archive.class = org.icatproject.ids.storage.ArchiveFileStorage
plugin.archive.properties = ids.storage_file.archive.properties
writeDelaySeconds = 60
startArchivingLevel1024bytes = 5000000
stopArchivingLevel1024bytes =  4000000
storageUnit = dataset
tidyBlockSize = 500

# File checking properties
filesCheck.parallelCount = 5
filesCheck.gapSeconds = 5
filesCheck.lastIdFile = /home/glassfish/icat/main/ids/lastIdFile
filesCheck.errorLog = /home/glassfish/icat/main/ids/errorLog

# Link properties
linkLifetimeSeconds = 3600

# JMS Logging
log.list = READ WRITE

# Uncomment to permit configuration of logback
!logback.xml = ids.logback.xml

# JMS - uncomment and edit if needed
!jms.topicConnectionFactory = java:comp/DefaultJMSConnectionFactory
```

Install the IDS
---------------
As before:
```Shell
./setup -vv install
```

Test the IDS
------------

We can check that the IDS is running by visiting the URL of the virtual machine (VM) followed by `/ids/ping`. In our tutorial, we can check this from inside the VM by using the following command:
```Shell
curl http://localhost:8080/ids/ping
```
which should output the text `IdsOK`.
