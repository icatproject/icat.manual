| Component | Version | Installation                                                                            | Documentation |
| --------- | ------- | ------------                                                                            | ------------- |
| topcat    | 2.4.8   | [Installation Guide](https://repo.icatproject.org/site/topcat/2.4.8/installation.html)  | |

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
curl -O https://repo.icatproject.org/repo/org/icatproject/topcat/2.4.8/topcat-2.4.8-distro.zip
cd ~/install
unzip ~/downloads/topcat-2.4.8-distro.zip
```

Configure the TopCat server
---------------------------

Create a new database for the TopCat server to use:

```Shell
mysqladmin --user=root --password=pw create topcat
```

Change directory into `topcat` and copy the `topcat-setup.properties.example` file to `topcat-setup.properties`. The first two sections of the file will be familiar - we tell the TopCat installer where to find MariaDB and Payara. Make sure to change the username, password and database url. Leave the email settings for now as we do not need them for this tutorial.
```INI
# MySQL
db.target      = mysql
db.driver      = com.mysql.jdbc.jdbc2.optional.MysqlDataSource
db.url         = jdbc:mysql://localhost:3306/topcat
db.username    = icatdbuser
db.password    = icatdbuserpw

secure = true
container = Glassfish
home = /home/glassfish/payara5
port = 4848
```

Copy the `topcat.properties.example` file to `topcat.properties`. We associate the `LILS` facility with the URLs of our services and the disable email alerts. We can leave the rest of the parameters to their defaults except the `adminUserNames`. We want to set the `adminUserNames` to the administration user account - in our case the `root` user configured in the `simple` authentication plugin.
```INI
facility.list = LILS

facility.LILS.icatUrl = https://localhost.localdomain:8181
facility.LILS.idsUrl = https://localhost.localdomain:8181

facility.LILS.downloadType.http = http://localhost.localdomain:8080
facility.LILS.downloadType.https = https://localhost.localdomain:8181

# enable send email
mail.enable = false

ids.getStatus.max = 100
poll.delay = 600
poll.interval.wait = 600

adminUserNames = simple/root
maxCacheSize=100000
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

    },
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
                },
            ],
            "downloadTransportTypes": [
                {
                    "type" : "http",
                    "idsUrl": "http://localhost.localdomain:18080"
                },
                {
                    "type" : "https",
                    "idsUrl": "https://localhost.localdomain:18181"
                },
            ]

...

        }
    ]
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
