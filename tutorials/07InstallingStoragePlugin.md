| Component         | Version | Installation                                                                                      | Documentation |
| ---------         | ------- | ------------                                                                                      | ------------- |
| ids.storage_file  | 1.3.3   | [Installation Guide](https://repo.icatproject.org/site/ids/storage_file/1.3.3/installation.html)  | [Component Documentation](https://icatproject.org/user-documentation/icat-data-service) |

Chapter 06: Install a Storage Plugin for the ICAT Data Service (IDS)
===================================================================

Overview
--------

The ICAT Data Service (IDS) controls access to the underlying data files which the ICAT Server catalogues and indexes. Those data files can be stored in a variety of different storage systems. The IDS uses plugins to provide access to the different storage systems. In this tutorial we will install the plugin which provides access to data stored in a normal file system.

A storage plugin will define one or two locations to store the data files: `main` and optionally `archive`. The `main` location is usually fast or low-latency storage such as a hard disk attached to the system. The `archive` location is slower, usually higher capacity storage such as tape. The IDS will manage copying files between the 2 locations to try to achieve the best download speed for the user.

Download and unzip
------------------
```Shell
wget http://www.icatproject.org/mvn/repo/org/icatproject/ids.storage_file/1.3.3/ids.storage_file-1.3.3-distro.zip
unzip ids.storage_file-1.3.3-distro.zip
```

Configure the Storage Plugin
----------------------------

First, we need to make directories to store the data files - one for `main` storage and one for `archive` storage. 
```Shell
mkdir -p /home/glassfish/icat/main
mkdir -p /home/glassfish/icat/archive
```

Next we configure the plugin to find the data storage directories created above:

In `ids.storage_file.main.properties`, set
```INI
dir=/home/glassfish/icat/main
```
In `ids.storage_file.archive.properties`, set
```INI
dir=/home/glassfish/icat/archive
```

In `ids.storage_file-setup.properties`, use the same settings as before:
```INI
#Glassfish
secure = false
container = Glassfish
home = /home/glassfish/glassfish4
port = 4848
```

Install the Storage Plugin
--------------------------
As before:
```Shell
./setup -vv install
```
