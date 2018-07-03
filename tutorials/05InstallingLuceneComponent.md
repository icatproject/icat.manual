| Component     | Version | Installation                                                                                  | Documentation |
| ---------     | ------- | ------------                                                                                  | ------------- |
| icat.lucene  | 1.1.0   | [Installation Guide](https://repo.icatproject.org/site/icat/lucene/1.1.0/installation.html)  | [Component Documentation](https://repo.icatproject.org/site/icat/lucene/1.1.0/) |

Chapter 05: Installing the Lucene Component
===========================================

Overview
--------

ICAT uses the Lucene library to allow users to search the metadata it holds. It used to be part of the ICAT Server, but, as of 4.9.0, Lucene search has its own component which has to be installed separately.

**NB: the commands on this page should be entered as the `glassfish` user**

Download and unzip
------------------

```Shell
cd downloads/
curl -O 'https://repo.icatproject.org/repo/org/icatproject/icat.lucene/1.1.0/icat.lucene-1.1.0-distro.zip'
cd ~/install
unzip ~/downloads/icat.lucene-1.1.0-distro.zip
```

Configure the Lucene component
------------------------------

Change directory to `icat.lucene` and use your favourite editor to create the `setup.properties` and `run.properties` as below.

setup.properties:
```INI
#Glassfish
secure = true
container = Glassfish
home = /home/glassfish/payara41
port = 4848
```

Payara encrypts secure connections using a self-signed certificate which is issued to the VM's hostname. The ICAT Lucene component (and others) is configured with a white-list of hosts which are allowed to access it. For this tutorial, we only want to allow the localhost to access the Lucene component so we must configure the white-list with the VM's hostname.

To find the VM's hostname, run:
```Shell
hostname
```
which outputs *localhost.localdomain* if you are using the recommended Vagrant VM.

Then edit run.properties:
```INI
directory     = /home/glassfish/data/lucene
commitSeconds = 5
ip            = <hostname from above>
```

Create the directory for Lucene to hold its data:
```Shell
mkdir -p ~/data/lucene
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

Also note in this file that the log file location is set to `${HOME}/logs/lucene.log`.

Install the Lucene component
----------------------------

```Shell
python setup -vv install
```

Check that it is running
------------------------

```Shell
asadmin list-applications
```
should output:
```
authn.simple-2.0.0  <ejb, web>
icat.lucene-1.1.0   <ejb, web>
Command list-applications executed successfully.
```
