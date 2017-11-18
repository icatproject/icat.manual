| Component     | Version | Installation                                                                                  | Documentation |
| ---------     | ------- | ------------                                                                                  | ------------- |
| authn.simple  | 1.1.0   | [Installation Guide](https://repo.icatproject.org/site/authn/simple/1.1.0/installation.html)  | [Component Documentation](https://repo.icatproject.org/site/authn/simple/1.1.0/index.html) |

Chapter 04: Installing an Authentication Plugin
==============================================

Overview
--------

ICAT relies on plugins to handle different methods of authenticating users to the system. There are 4 plugins available: an anonymous plugin allowing anonymous users; a simple plugin allowing a list of usernames and passwords to be defined at install time; an LDAP plugin to connect to an LDAP server and a database plugin which re-uses the relational database to store the usernames and passwords. In this tutorial, we will use the simple authentication plugin. However, it is worth noting that most facilities who use the ICAT software write their own plugin. 

Download and unzip
------------------

```Shell
wget http://www.icatproject.org/mvn/repo/org/icatproject/authn.simple/1.1.0/authn.simple-1.1.0-distro.zip
unzip authn.simple-1.1.0-distro.zip
```

Configure the authentication plugin
-----------------------------------

Change directory to `authn_simple` and use your favourite editor to configure the `authn_simple-setup.properties` file. Since this is just a test system, we do not need to configure secure access so set `secure` to `false`. The `container` in this tutorial is `Glassfish` (note the case) and the `home` points to the location of the GlassFish installation, in this tutorial it is `/home/glassfish/glassfish4`. Finally we set the port for communication with the GlassFish server.

```INI
#Glassfish
secure = false
container = Glassfish
home = /home/glassfish/glassfish4
port = 4848
```

In the same directory, you will need to configure the list of usernames and passwords in the `authn_simple.properties` file. For this tutorial, we will define 3 users: root, ingest and stuart. The `mechanism` option is used to label this authentication mechanism and is useful when there is more than one authentication plugin installed. For example, if there was a user called `stuart` in the simple authenticator *and* the db authenticator, then we would refer to the user in the simple authenticator as `simple/stuart` and the user in the db authenticator as `db/stuart`. In this way we can be clear which user we mean. In some ICAT documentation, the `mechanism` is referred to as `mnemonic`. The two terms can be used interchangably.

```Shell
#User list
user.list = root ingest stuart

user.root.password = pw
user.ingest.password = ingest
user.stuart.password = stuart

mechanism = simple
```

Install the authentication plugin
---------------------------------

Installing most ICAT components consists running `./setup install`. We use the `-vv` flag here to turn on extra verbosity so that we can see what the setup script is doing.

```Shell
./setup -vv install
```
