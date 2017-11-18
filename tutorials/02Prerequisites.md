| Component | Version | Installation                                            | Documentation                                                         |
| --------- | ------- | ------------                                            | -------------                                                         |
| Java      | 1.8.0   | [Installation Guide](http://openjdk.java.net/install/)  | [ICAT Documentation](https://icatproject.org/installation/glassfish/) |
| MySQL     | 5.1     | -                                                       | [ICAT Documentation](https://icatproject.org/installation/database/) - [Official Documentation](https://docs.oracle.com/cd/E19078-01/mysql/mysql-refman-5.1/) |

Chapter 02: Prerequisites
========================

Overview
--------

The ICAT software is written in the Java programming language with some additional tools written in Python. The application runs in a Java application server and uses a relational database to store its data. In this tutorial we will be installing the GlassFish Java application server and the MySQL database. 

All of the commands on this page should be entered as the `root` user so let's change to the `root` user:
```Shell
sudo -i
```

Install Java
------------

ICAT requires at least JDK verion 1.8. Since ICAT is distributed online as a collection of .zip files, we will also need to install curl, wget and unzip (on the minimal linux operating system used for this tutorial - other OSes may have them pre-installed).

```Shell
yum install java-1.8.0-openjdk-headless curl wget unzip
```

Install and configure MySQL
---------------------------

We install MySQL, the MySQL java connector and some python dependencies.
 
```Shell
yum install mysql-server mysql MySQL-python python-suds python-requests mysql-connector-java
```

Edit the MySQL configuration file at `/etc/my.cnf`. In the `[mysqld]` section, set the default storage engine to InnoDB.

```INI
[mysqld]
default_storage_engine = InnoDB
```

Start the MySQL database then run `mysql-secure-installation` to make it more secure. Enter the details when prompted. In this tutorial, we assume the database root user has the password 'pw'.

```Shell
service mysqld start
mysql-secure-installation
```

Create the database and user for ICAT to use. For this tutorial, the ICAT database, the database user and the password are all 'icat'.
```Shell
mysqladmin --user=root --password=pw create icat
mysql --user=root --password=pw --execute="CREATE USER 'icat'@'localhost' IDENTIFIED BY 'icat';"
mysql --user=root --password=pw --execute="GRANT ALL PRIVILEGES ON *.* TO 'icat'@'localhost';"
```

Optionally install useful tools
-------------------------------
You may like to install you favourite editor, the man program and a terminal screen multiplexer eg.
```Shell
yum install vim man screen
```

Create the GlassFish user
-------------------------

The GlassFish Application Server should be run under its own account so let's make one.

```Shell
useradd glassfish
```

For the rest of this tutorial, it is assumed that you will use the `glassfish` user account, unless stated otherwise, so let's switch to it.
```Shell
su glassfish
```