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
# Glassfish
secure = true
container = Glassfish
home = /home/glassfish/payara41
port = 4848
```

The ICAT Lucene component (and others) is configured with a white-list of ip addresses or address ranges which are allowed to access it. For this tutorial, we want to allow access from the other components running on the VM and the browser running on your desktop. To achieve this, we configure the `ip` parameter in `run.properties` with the addresses we need. The example below shows the ipv4 localhost, the ipv6 localhost and the 10.x.x.x range which is used by the Vagrant VM for the address of the host computer.

Edit run.properties:
```INI
# Real comments in this file are marked with '#' whereas commented out lines
# are marked with '!'

directory = /home/glassfish/data/lucene
commitSeconds = 5
ip = 127.0.0.1/32 0:0:0:0:0:0:0:1/128 10.0.0.1/8
```
**Note: IP addresses or ranges (ipv4 and ipv6) must be written in full followed by a '/' and the number of bits to consider. For example, you cannot use the abbreviated `::1` form for the ipv6 localhost address.**

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
icat.lucene-1.1.0   <ejb, web>
Command list-applications executed successfully.
```

Check access on the host
------------------------

You can test that the authentication plugin is running using the `cURL` tool that we use to download packages.

To test ipv4 connections:
```Shell
curl -k -4 'https://127.0.0.1:8181/icat.lucene/version'
```
and to test ipv6 connections:
```Shell
curl -k -6 -g 'https://[::1]:8181/icat.lucene/version'
```
should both return:
```JSON
{"version":"1.1.0"}
```

(curl options: `-k` tells curl to ignore untrusted certificates; `-4/-6` tells curl which protocol version to use; `[::1]` is the short version of the ipv6 local address; `-g` avoids confusing older versions of curl with ipv6 addresses.)

Check access from your browser
------------------------------

You can check that the Lucene component is running in your browser. As before, you will have to click through to accept the connection to an untrusted site.

If you have followed the recommended Vagrant set up, then port `18181` on your host machine will be  mapped to port `8181` on the VM.

(https://localhost:18181/icat.lucene/version)

will return:
```JSON
{"version":"1.1.0"}
```
