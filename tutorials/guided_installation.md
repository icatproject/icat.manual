# Guided Installation

TODO: Include content from https://github.com/stuartpullinger/icat_install/blob/master/icat_install_tutorial/00Introduction.md in this file

This tutorial will lead you through the process of installing and configuring a test installation of the ICAT software.

## Aim

The aim of this tutorial is to give the reader a introduction to the ICAT software and the process of installing and configuring it. The result of following this tutorial will be a system with enough of the ICAT components installed to allow the user to test the functionality of the software. It is important to note that the resulting system will not be fault-tolerant or secure enough to use in a production environment. It should, however, be sufficient to allow the user to load some data and interact with it to understand how the components relate to one another.

## Before You Start

This tutorial assumes that you have access to a system (or virtual machine) with a RedHat-derived version of GNU/Linux installed, such as CentOS, and that you are familiar with system administration on this type of operating system. This tutorial was tested on a minimal installation of CentOS version 6.6. If you are unsure which software to use to create a virtual machine, several ICAT developers have had success with the combination of VirtualBox and Vagrant. See the next optional chapter for instruction on how to do this.

## Software Versions

Before we start installing the ICAT Software, we will install the prerequisites: OpenJDK Java, the GlassFish application server which hosts the software and the MySQL database. Other versions of Java will work with the ICAT Software including Oracle's [Java](http://www.oracle.com/technetwork/indexes/downloads/index.html). The ICAT software can also be used with the [Payara](https://www.payara.fish/) application server. (*N.B. Future versions of this tutorial will use Payara*). Finally, it is also possible to use an Oracle [database](https://www.oracle.com/database/index.html) in place of MySQL.

The first component we will install is an authentication plugin which will handle authenticating users to all the subsequent components. Next to be installed will be the ICAT Server which authenticates users, allows access to the data catalogue through its APIs and coordinates the ICAT Data Service. A storage plugin will be installed to provide access to the file system for the ICAT Data Service (IDS). The IDS controls access to the data files in the ICAT catalogue and will be installed next. After creating some test data, we will install the TopCat web interface which provides an easy to use interface to the ICAT Software.

At the time of writing this tutorial, the current stable version of the ICAT Server - the main component of the ICAT software - is 4.8.0. This will be the version installed in this tutorial along with its dependencies. The full list of software and components installed in this tutorial are listed below:

- OpenJDK Java 1.8.0
- GlassFish 4.0
- MySQL 5.1
- Simple Authenticator [1.1.0](https://repo.icatproject.org/site/authn/simple/1.1.0/)
- ICAT Server [4.8.0](https://repo.icatproject.org/site/icat/server/4.8.0/)
- IDS Storage File plugin [1.3.3](https://repo.icatproject.org/site/ids/storage_file/1.3.3/)
- ICAT Data Server (IDS) [1.7.0](https://repo.icatproject.org/site/ids/server/1.7.0/)
- TopCat [2.2.1](https://repo.icatproject.org/site/topcat/2.2.1/)

TODO: Import the rest of the tutorial files into this directory, removing the 'Chapter XX' parts of their titles.
