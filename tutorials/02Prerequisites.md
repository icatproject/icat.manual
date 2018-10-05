| Component | Version | Installation                                            | Documentation                                                         |
| --------- | ------- | ------------                                            | -------------                                                         |
| Java      | 1.8.0   | [Installation Guide](http://openjdk.java.net/install/)  | [ICAT Documentation](https://icatproject.org/installation/glassfish/) |
| MariaDB (MySQL)     | 5.5.56     | -                                                       | [ICAT Documentation](https://icatproject.org/installation/database/) - [Official Documentation](https://mariadb.com/kb/en/library/documentation/) |

Chapter 02: Prerequisites
========================

Overview
--------

The ICAT software is written in the Java programming language with some additional tools written in Python. The application runs in a Java application server and uses a relational database to store its data. In this chapter we will be installing the open-source Java distribution provided by OpnJDK and the MariaDB database. MariaDB is the community-developed fork of Oracle's MySQL database. In the next chapter we will install the Payara application server. Payara is the open-source fork of Oracle's Glassfish application server.

All of the commands on this page should be entered as the `root` user so let's change to the `root` user:
```Shell
[vagrant@localhost ~]$ sudo -i
```

Install Java
------------

ICAT requires at least JDK verion 1.8. Since ICAT is distributed online as a collection of .zip files, we will also need to install unzip.

```Shell
[root@localhost ~]# yum install java-1.8.0-openjdk-headless java-1.8.0-openjdk-devel unzip
```

Install and configure MariaDB
-----------------------------

We install MariaDB, the MySQL java connector and some python dependencies.
 
```Shell
[root@localhost ~]# yum install mariadb-server mariadb MySQL-python python-suds python-requests mysql-connector-java
```

Edit the MySQL configuration file at `/etc/my.cnf`. In the `[mysqld]` section, set the default storage engine to InnoDB.

```INI
[mysqld]
default_storage_engine = InnoDB
```

Start the MariaDB database, enable to run on boot then run `mysql-secure-installation` to make it more secure. Enter the details when prompted. In this tutorial, we assume the database root user has the password 'pw'.

```Shell
[root@localhost ~]# systemctl start mariadb.service
[root@localhost ~]# systemctl enable mariadb.service
[root@localhost ~]# mysql_secure_installation
```

Create the database and user for ICAT to use. For this tutorial, the ICAT database is 'icatdb', the database user is 'icatdbuser' and the password is 'icatdbuserpw'.
```Shell
[root@localhost ~]# mysqladmin --user=root --password=pw create icatdb
[root@localhost ~]# mysql --user=root --password=pw --execute="CREATE USER 'icatdbuser'@'localhost' IDENTIFIED BY 'icatdbuserpw';"
[root@localhost ~]# mysql --user=root --password=pw --execute="GRANT ALL PRIVILEGES ON *.* TO 'icatdbuser'@'localhost';"
```

Optionally install useful tools
-------------------------------
You may like to install you favourite editor and a terminal screen multiplexer eg.
```Shell
[root@localhost ~]# yum install man vim screen
```

Create the GlassFish user
-------------------------

The Payara Application Server should be run under its own account so let's make one. We use `glassfish` as the account name because has historically been the default in the ICAT software.

```Shell
[root@localhost ~]# adduser glassfish
```

For the rest of this tutorial, it is assumed that you will use the `glassfish` user account, unless stated otherwise, so let's switch to it.
```Shell
[root@localhost ~]# su - glassfish
```
