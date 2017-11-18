| Component | Version | Installation                                                                            | Documentation |
| --------- | ------- | ------------                                                                            | ------------- |
| topcat    | 2.2.1   | [Installation Guide](https://repo.icatproject.org/site/topcat/2.2.1/installation.html)  | [Component Documentation](https://icatproject.org/user-documentation/icat-data-service/) |

Chapter 09: Install the TopCat Web Interface
===========================================

Overview
--------

TopCat is the easy to use front-end to the ICAT catalogue. It provides the scientisits with a familiar web interface to find and download their data. It uses the ICAT and IDS APIs to access authentication, the data catalogue and the data files. In the same way as the previous components, it runs inside the GlassFish application server so the installation procedure will be familiar. It is necessary to have some test data in your ICAT system before installing TopCat so you should have followed the procedure to create some test data from the previous chapter. Finally, it is a presumption of the way TopCat works that you will install one instance of TopCat for each facility that you have in you ICAT database. If you have followed the previous chapter, you will have a facility called LILS in your database. LILS stands for Lorum Ipsum Light Source and is a pun on the [Diamond Light Source](http://www.diamond.ac.uk) who use the ICAT software.

Download and unzip TopCat
-------------------------

```Shell
wget https://repo.icatproject.org/repo/org/icatproject/topcat/2.2.1/topcat-2.2.1-distro.zip
unzip topcat-2.2.1-distro.zip
```

Configure TopCat
----------------

The first two sections of the `topcat-setup.properties` file will be familiar - we tell the TopCat installer where to find GlassFish and MySQL. Finally, we disable email alerts as we don't need them for this tutorial.
```INI
#Glassfish
secure = false
container = Glassfish
home = /home/glassfish/glassfish4
port = 4848

# MySQL
db.target      = mysql
db.driver      = com.mysql.jdbc.jdbc2.optional.MysqlDataSource
db.url         = jdbc:mysql://localhost:3306/icat
db.username    = icat
db.password    = icat

# Email setup
mail.enable = false
```

In the `topcat.properties` file, we disable email alerts again (*N.B. Later versions of TopCat will only configure email in this file*). We can leave the rest of the parameters to their defaults except the `adminUserNames`. We want to set the `adminUserNames` to the administration user account - in our case the `root` user configured in the `simple` authentication plugin.
```INI
# Email setup
mail.enable = false

ids.getStatus.max = 100
poll.delay = 600
poll.interval.wait = 600

adminUserNames = simple/root
```

There are 3 other files to configure. The `topcat.json` file contains settings specific to this site. We just need to change `https` to `http` and the port from `8181` to `8080` wherever it occurs in the file since we are not using secure communication. We also need to edit the facility name in the file to match the facility created by the test data - 'LILS' which stands for Lorum Ipsum Light Source. The `lang.json` file contains the text used to create the web interface elements - such as the text 'Download' on the download button. It is safe to just copy the example file. The `topcat.css` file allows us to change the style of the TopCat interface. Again, it is safe to just copy the example file

```Shell
sed -e 's/https/http/g; s/8181/8080/g; s/your facility name[^"]*/LILS/g' topcat.json.example >topcat.json
cp lang.json.example lang.json
cp topcat.css.example topcat.css
```

Install TopCat
--------------
As before
```Shell
./setup -vv install
```
