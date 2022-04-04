| Component         | Version | Installation                                                                                      | Documentation |
| ---------         | ------- | ------------                                                                                      | ------------- |
| ids.storage_file  | 1.4.3   | [Installation Guide](https://repo.icatproject.org/site/ids/storage_file/1.4.3/installation.html)  | [Component Documentation](https://icatproject.org/user-documentation/icat-data-service) |

Chapter 07: Install a Storage Plugin for the ICAT Data Service (IDS)
===================================================================

Overview
--------

The ICAT Data Service (IDS) controls access to the underlying data files which the ICAT Server catalogues and indexes. Those data files can be stored in a variety of different storage systems. The IDS uses plugins to provide access to the different storage systems. In this tutorial we will install the plugin which provides access to data stored in a normal file system.

A storage plugin will define one or two locations to store the data files: `main` and optionally `archive`. The `main` location is usually fast or low-latency storage such as a hard disk attached to the system. The `archive` location is slower, usually higher capacity storage such as tape. The IDS will manage copying files between the 2 locations to try to achieve the best download speed for the user.

Download and unzip
------------------
```Shell
cd ~/downloads
curl -O 'https://repo.icatproject.org/repo/org/icatproject/ids.storage_file/1.4.3/ids.storage_file-1.4.3-distro.zip'
cd ~/install
unzip ~/downloads/ids.storage_file-1.4.3-distro.zip
```

Configure the Storage Plugin
----------------------------

Change directory to the `ids.storage_file` directory. Create `setup.properties` and use the same settings as before:
```INI
#Glassfish
secure = true
container = Glassfish
home = /home/glassfish/payara5
port = 4848
```

Configure Logging
-----------------

At the time of writing, the plugin does not write to any log files, so there is no logging configuration. There is an open issue for this [here](https://github.com/icatproject/ids.server/issues/76).

Install the Storage Plugin
--------------------------
As before:
```Shell
./setup -vv install
```


