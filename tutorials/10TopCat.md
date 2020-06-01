| Component | Version | Installation                                                                            | Documentation |
| --------- | ------- | ------------                                                                            | ------------- |
| topcat    | 2.4.0   | [Installation Guide](https://repo.icatproject.org/site/topcat/2.4.0/installation.html)  | |

Chapter 10: Install the TopCat Web Interface
===========================================

Overview
--------

TopCat is the front-end to the ICAT catalogue. It provides the scientists with a familiar web interface to find and download their data. It uses the ICAT and IDS APIs to access authentication, the data catalogue and the data files. It has 2 components: a server which runs inside the Payara application server; and a javascript client which runs in the user's browser. Therefore, there are 2 sets of configuration files.

It is necessary to have some test data in your ICAT system before installing TopCat so you should have followed the procedure to create some test data from the previous chapter and have a facility called LILS in your database.

Download and unzip TopCat
-------------------------

```Shell
cd ~/downloads
curl -O https://repo.icatproject.org/repo/org/icatproject/topcat/2.4.0/topcat-2.4.0-distro.zip
cd ~/install
unzip ~/downloads/topcat-2.4.0-distro.zip
```

Configure the TopCat server
---------------------------

Create the database and user for the TopCat server to use. For this tutorial, the TopCat database is 'topcatdb', the database user is 'topcatdbuser' and the password is '$TOPCAT_DB_PASSWD'.

```Shell
[root@localhost ~]# mysql -u root -p -h localhost
```
```SQL
MariaDB [(none)]> CREATE DATABASE topcatdb;
MariaDB [(none)]> CREATE USER 'topcatdbuser'@'localhost' IDENTIFIED BY '$DB_TOPCAT_PASSWD';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON topcatdb.* TO 'topcatdbuser'@'localhost';
MariaDB [(none)]> QUIT;
```

Change directory into `topcat` and copy the `topcat-setup.properties.example` file to `topcat-setup.properties`. The first two sections of the file will be familiar - we tell the TopCat installer where to find MariaDB and Payara. Make sure to change the username, password and database url. Leave the email settings for now as we do not need them for this tutorial.
```INI
# Glassfish
secure = true
container = Glassfish
home = /home/glassfish/payara41
port = 4848

# MySQL
db.target = mysql
db.driver = com.mysql.jdbc.jdbc2.optional.MysqlDataSource
db.url = jdbc:mysql://localhost:3306/topcatdb
db.username = topcatdbuser
db.password = $TOPCAT_DB_PASSWD

# Email setup see http://docs.oracle.com/cd/E26576_01/doc.312/e24938/create-javamail-resource.htm#GSRFM00035
mail.host = smtp.example.com
mail.user = user@example.com
mail.from = no-reply@example.com

# See https://javamail.java.net/nonav/docs/api/ for list of properties
mail.property = mail.smtp.port="25":mail.smtp.from="no-reply@example.com"
```

Copy the `topcat.properties.example` file to `topcat.properties`. We associate the `LILS` facility with the URLs of our services and the disable email alerts. We can leave the rest of the parameters to their defaults except the `adminUserNames`. We want to set the `adminUserNames` to the administration user account - in our case the `root` user configured in the `simple` authentication plugin.
```INI
# List of Facility names
# These names will be used by Topcat over the REST API;
# each facility name in topcat.json must contain a match in this list,
# and each name must be mapped to ICAT / IDS urls.
# Edit these values to match your installation.

facility.list = LILS

facility.LILS.icatUrl = https://localhost.localdomain:8181
facility.LILS.idsUrl = https://localhost.localdomain:8181

# Download transport URLs
# topcat.json can specify one or more download transport types for each facility, egs "https", "globus";
# each may have a distinct download URL (prefix). To specify the download URL for a specific facility
# and transport type, set an appropriate property as below.
# Note that the transport type will be set in requests from the Topcat Javascript application,
# but the URL to specify here should be the IDS url that will be used in the Java clients;
# so it may be that the Javascript transport type https should be mapped here to an
# internal http URL.
# If the property is not defined, Topcat will use the facility's idsUrl by default.

facility.LILS.downloadType.http = http://localhost.localdomain:8080
facility.LILS.downloadType.https = https://localhost.localdomain:8181

# enable send email
mail.enable = false

# The email body message for https downloads. All subject tokens as above are available.
mail.body.https = Hi ${userName},\n\nYour ${size} download ${fileName} has been prepared and is ready for download at ${downloadUrl}.\n\nThank you for using TopCAT.

# The email body message for https downloads. All subject tokens as above are available.
mail.body.globus = Hi ${userName}, \n\nYour ${size} Globus download ${fileName} is ready. Please see https:/example.com/#/globus-faq for more information on how to download using Globus.\n\nThank you for using TopCAT

# The email body message for smartclient downloads. All subject tokens as above are available.
mail.body.smartclient = Hi ${userName}, \n\nYour ${size} SmartClient download ${fileName} is ready. Please check your smartclient home directory for your files.\n\nThank you for using TopCAT

# The email body message for SCARF downloads. All subject tokens as above are available.
mail.body.scarf = Hi ${userName}, \n\nYour ${size} SCARF download ${fileName} is ready. Please see https:/example.com/#/scarf-faq for more information on how to download using SCARF.\n\nThank you for using TopCAT

# The maximum number of datafiles for a getStatus call to the IDS for two level storage
ids.getStatus.max = 100

# The delay in seconds before polling starts. This delay is to gives the ids a chance to do its thing before we query it
poll.delay = 600

# The wait time in seconds between each poll to the IDS.
poll.interval.wait = 600

# A list of usernames that can use the admin REST API and Topcat admin user interface
adminUserNames = simple/root

# The maximum number objects that can be cached before pruning will take place
maxCacheSize = 100000
```

Configure the TopCat javascript client
--------------------------------------

There are 3 files to configure for the javascript client. The `topcat.json` file contains settings specific to this site. The `lang.json` file contains the text used to create the web interface elements - such as the text 'Download' on the download button. The `topcat.css` file allows us to change the style of the TopCat interface.

We only need to change the `topcat.json` file for this tutorial. As above, we need to configure the URLs for the IDS and ICAT servers, and the URLs for the download types. However, we may need to use different URLs. Remember that the `topcat.json` file tells the javascript client (running in the user's browser) where to find the ICAT services. If you have followed the recommended Vagrant setup, the ports 8080 and 8181 in the VM will be mapped to 18080 and 18181 on the host machine. Therefore, we need to use the 18XXX ports in the URLS in the `topcat.json` as this is how the browser running on the host will access the services. Copy the `topcat.json.example` file to `topcat.json` and find line 202 in your editor and configure the URLs:

```JSON
{
    "site": {
        "topcatUrl": "https://localhost.localdomain:18181",

...

    "facilities": [
        {
            "name": "LILS",
            "title": "Lorum Ipsum Light Source",
            "idsUrl": "https://localhost.localdomain:18181",
            "icatUrl": "https://localhost.localdomain:18181",

...

            "authenticationTypes": [
                {
                    "title": "Simple",
                    "plugin": "simple"
                }
            ],
            "downloadTransportTypes": [
                {
                    "type" : "http",
                    "idsUrl": "http://localhost.localdomain:18080"
                },
                {
                    "type" : "https",
                    "idsUrl": "https://localhost.localdomain:18181"
                }
            ],

...

        }
    ],
    "plugins": []
}
```

Copy the other files:
```Shell
cp lang.json.example lang.json
cp topcat.css.example topcat.css
```

Configure verbose logging
-------------------------

```Shell
cp logback.xml.example logback.xml
```

Change the location for the log files from a relative path starting with `..` to an absolute path starting with `${HOME}`. This means we will find the `topcat.log` file in the same directory as the other components' log files.

Change
```XML
                <file>../logs/topcat.log</file>
                <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
                        <fileNamePattern>../logs/topcat.log.%d{yyyy-MM-dd}.%i.zip
```
to
```XML
                <file>${HOME}/logs/topcat.log</file>
                <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
                        <fileNamePattern>${HOME}/logs/topcat.log.%d{yyyy-MM-dd}.%i.zip
```

Configure **DEBUG** level logging to aid in troubleshooting.


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

Copy the example content
------------------------

The TopCat interface has pages for 'About', 'Contact' and 'Help'. The contents for these pages is in the `content.example` directory. Copy this to the `content` directory so the installer can find it and include it in the deployment.

```Shell
cp -r content.example content
```

**Note: if you do not to copy the content directory, the TopCat interface may fail to load.**

Install TopCat
--------------
As before
```Shell
./setup -vv install
```

Log in to TopCat
----------------

If you have followed all the steps in this tutorial successfully, you should have a working installation of the ICAT software loaded with test data. If you have used the suggested Vagrant setup from this tutorial you may now access your TopCat installation from your web browser by visiting the following URL:

(https://localhost.localdomain:18181/)

selecting 'Lorum Ipsum Light Source' as the facility and entering 'root' and 'pw' as the username and password respectively.

You will be able to click around the interface, but remember that we have only added randomly-generated metadata. There are no real files managed by the IDS so you will not be able to download anything.
