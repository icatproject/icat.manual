**This chapter is intended to replace 08TestData so please don't run both**

Chapter 08: Create Some Test Data With Python ICAT
================================

Overview
--------

Usually, data is inputted to ICAT (ingested) from automated scripts running at the scientific facility which is running the instance of ICAT. The scripts extract metadata from the facility booking system, the experimental equipment control systems and the resulting data files and load it into ICAT for cataloging and indexing using one of the APIs - ReST or SOAP - along with the location of the data files. Setting all this up is beyond the scope of this tutorial.

**ADD TEXT EXPLAINING PYTHON ICAT HERE**

Install Python-ICAT
------------------------

First we need to load some dependencies. The full list can be found [here](https://github.com/icatproject/python-icat), but we only need some of them as the ingest script is all we want to run.

```Shell
yum install python-pip python-argparse python-lxml
pip install suds-jurko PyYAML
```
*N.B.The first command will need to be run by root or a sudo user*

Next we need to download and install python-icat.

```Shell
wget https://icatproject.org/misc/python-icat/download/python-icat-0.13.1.tar.gz
unzip python-icat-0.13.1.tar.gz
```

Navigate to the python-ICAT directory.
``` Shell
cd python-icat-0.13.1
```

If you are running python 2.6, apply the python2_6.patch before continuing.
```Shell
patch -p1 < python2_6.patch
```

Build then install Python-ICAT.
```Shell
python setup.py build
python setup.py install
```

*N.B. The install command may need to be run as root or as sudo user.*

Create and load some test data
-----------------------------

We will need some sample data to ingest into the database. Download the sample file.

```Shell
wget https://raw.githubusercontent.com/JHaydock-Pro/lils.yml/master/lils.yml
```

Now we need to ingest it. As the user with icat installed, run the ingest script.

```Shell
icatingest.py --url http://localhost:8080/ICATService/ICAT?wsdl -u root -p pw -i lils.yml -a simple
```

*N.B. The login information refers the authentication plugin we have chosen not the system or mysql user*

*N.B. This sample file and it's url are placeholder until a better one is created. It will run for a long time, it is safe to kill it if it runs to long. ~1min is needed to ingest a reasonable amount of data.*
