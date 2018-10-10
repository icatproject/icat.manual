# Guided Installation

This tutorial will lead you through the process of installing and configuring a test installation of the ICAT software.

## Aim

The aim of this tutorial is to give the reader a introduction to the ICAT software and the process of installing and configuring it. The result of following this tutorial will be a system with enough of the ICAT components installed to allow the user to test the functionality of the software. It is important to note that the resulting system will not be fault-tolerant or secure enough to use in a production environment. It should, however, be sufficient to allow the user to load some data and interact with it to understand how the components relate to one another.

## Before You Start

This tutorial assumes that you have access to a system (or virtual machine) with a RedHat-derived version of GNU/Linux installed, such as CentOS, and that you are familiar with system administration on this type of operating system. This tutorial was tested on a virtual machine image of CentOS 7. If you are unsure which software to use to create a virtual machine, several ICAT developers have had success with the combination of VirtualBox and Vagrant. See the next chapter for instructions on how to do this.

## Software Versions

Before we start installing the ICAT Software, we will install the prerequisites: OpenJDK Java, the Payara application server (the successor to Glassfish) which hosts the software and the MariaDB database (the successor to MySQL). Other versions of Java will work with the ICAT Software including Oracle's [Java](http://www.oracle.com/technetwork/indexes/downloads/index.html). Finally, it is also possible to use an Oracle [database](https://www.oracle.com/database/index.html) in place of MariaDB/MySQL.

The first component we will install is an authentication plugin which will handle authenticating users to all the subsequent components. Next to be installed will be the ICAT Server which authenticates users, allows access to the data catalogue through its APIs and coordinates the ICAT Data Service. A storage plugin will be installed to provide access to the file system for the ICAT Data Service (IDS). The IDS controls access to the data files in the ICAT catalogue and will be installed next. After creating some test data, we will install the TopCat web interface which provides an easy to use interface to the ICAT Software.

At the time of writing this tutorial, the current stable version of the ICAT Server - the main component of the ICAT software - is 4.9.1. This will be the version installed in this tutorial along with its dependencies. The full list of software and components installed in this tutorial are listed below:

- OpenJDK Java 1.8.0
- Payara 4.1.2.181
- MariaDB 5.5.56
- Simple Authenticator [2.0.0](https://repo.icatproject.org/site/authn/simple/2.0.0/)
- ICAT Server [4.9.1](https://repo.icatproject.org/site/icat/server/4.9.1/)
- IDS Storage File plugin [1.4.1](https://repo.icatproject.org/site/ids/storage_file/1.4.1/)
- ICAT Data Server (IDS) [1.9.0](https://repo.icatproject.org/site/ids/server/1.9.0/)
- TopCat [2.4.0](https://repo.icatproject.org/site/topcat/2.4.0/)
