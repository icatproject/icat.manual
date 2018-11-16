| Component     | Version | Installation                                                                                  | Documentation |
| ---------     | ------- | ------------                                                                                  | ------------- |
| authn.simple  | 2.0.0   | [Installation Guide](https://repo.icatproject.org/site/authn/simple/2.0.0/installation.html)  | [Component Documentation](https://repo.icatproject.org/site/authn/simple/2.0.0/index.html) |

Chapter 04: Installing an Authentication Plugin
==============================================

Overview
--------

ICAT relies on plugins to handle different methods of authenticating users to the system. There are 4 plugins available: an anonymous plugin allowing anonymous users; a simple plugin allowing a list of usernames and passwords to be defined at install time; an LDAP plugin to connect to an LDAP server and a database plugin which re-uses the relational database to store the usernames and passwords. In this tutorial, we will use the simple authentication plugin. However, it is worth noting that most facilities who use the ICAT software customize the authentication process.

**NB: the commands on this page should be entered as the `glassfish` user**

Download and unzip
------------------

```Shell
cd downloads/
curl -O 'https://repo.icatproject.org/repo/org/icatproject/authn.simple/2.0.0/authn.simple-2.0.0-distro.zip'
cd ~/install
unzip ~/downloads/authn.simple-2.0.0-distro.zip
```

Configure the authentication plugin
-----------------------------------

Change directory to `authn.simple` and use your favourite editor to create the `setup.properties` file. The `container` in this tutorial is `Glassfish` (note the case) - since Payara is a fork of Glassfish - and the `home` points to the location of the Payara installation. In this tutorial it is `/home/glassfish/payara41`. Finally we set the port for communication with the Payara server.

```INI
#Glassfish
secure = true
container = Glassfish
home = /home/glassfish/payara41
port = 4848
```

In the same directory, you will need to configure the list of usernames and passwords in the `run.properties` file. For this tutorial, we will define 4 accounts: root, ingest, reader and icatuser to model the different types of account you might have on a real installation. The 'root' account is for administration; the 'ingest' account will have write access to the database to allow data to be ingested to the system; the 'reader' account will have read access to the database to allow ICAT Data Server to query the database; and the 'icatuser' account simulates a user who may have more complicated permissions depending on membership of various groups.

The `mechanism` option is used to label this authentication mechanism and is useful when there is more than one authentication plugin installed. For example, if there was a user called `stuart` in the simple authenticator *and* the db authenticator, then we would refer to the account in the simple authenticator as `simple/stuart` and the account in the db authenticator as `db/stuart`. In this way we can be clear which account we mean. In some ICAT documentation, the `mechanism` is referred to as `mnemonic`. The two terms can be used interchangably.

```Shell
#User list
user.list = root ingest reader icatuser

user.root.password = rootpw
user.ingest.password = ingestpw
user.reader.password = readerpw
user.icatuser.password = icatuserpw

mechanism = simple
```

Configure verbose logging
-------------------------

Since you may come up against problems, we will configure **DEBUG** level logging to aid in troubleshooting. Copy the `logback.xml.example` file to a new file and change the log level to `DEBUG`.


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

Also note in this file that the log file location is set to `${HOME}/logs/authn_simple.log`.

Install the authentication plugin
---------------------------------

Installing most ICAT components consists of running `./setup install`. We use the `-vv` flag here to turn on extra verbosity so that we can see what the setup script is doing.

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
Command list-applications executed successfully.
```

Check access from your browser
------------------------------

You can check that the plugin is accessible from your browser. You will have to click through to accept the connection to an untrusted site.

If you have followed the recommended Vagrant set up, then port `18181` on your host machine will be  mapped to port `8181` on the VM.

(https://localhost:18181/authn.simple/version)

will return:
```JSON
{"version":"2.0.0"}
```
