Chapter 08: Ingest Some Test Data
=================================

Overview
--------

Usually, data is inputted to ICAT (ingested) from automated scripts running at the scientific facility which is running the instance of ICAT. The scripts extract metadata from the facility booking system, the experimental equipment control systems and the resulting data files and load it into ICAT for cataloging and indexing using one of the APIs - ReST or SOAP - along with the location of the data files. Setting all this up is beyond the scope of this tutorial. Fortunately, there is a script which comes with the Python interface to ICAT which will ingest sample data from a YAML file. In this chapter, we will install the Python interface to ICAT and download a YAML file containing some randomly-generated data so that we can test the web interface to ICAT.

Install dependencies
--------------------

After the operating system repositories, the preferred way to install Python packages is using the `pip` installer tool. Many operating systems come with pip pre-installed or in the repositories. For the VM used in this tutorial, we have to enable the `EPEL` repository before we can get `pip`.

```Shell
sudo yum install epel-release
sudo yum check-update
sudo yum install python2-pip
```

Then use `pip` to install the dependencies for the Python interface to ICAT into the `glassfish` user's home directory.
```Shell
pip install --user --upgrade pip setuptools
pip install --user suds-jurko PyYAML
```
**Note: Make sure you install `suds-jurko` and not `suds`. `suds-jurko` is a fork of `suds` which is no longer maintained.**

Download and install python-icat
--------------------------------

As before, download and uncompress the package:
```Shell
cd ~/downloads
curl -O 'https://icatproject.org/misc/python-icat/download/python-icat-0.14.1.tar.gz'
cd ~/install
tar xvzf ../downloads/python-icat-0.14.1.tar.gz
```

We have to build the package before installing it. We use `pip` to install it to the same location as above.
```Shell
cd python-icat-0.14.1
python setup.py build
pip install --user .
```

Download and install the test data
----------------------------------

Some data has already been randomly-generated for running tests in the `icat.ansible` installer package so we download this:

```Shell
cd ~/scripts
curl -O 'https://raw.githubusercontent.com/icatproject-contrib/icat.ansible/master/files/lils.yml'
```

The python-icat package comes with a script called `icatingest.py` which we can use with the file we just downloaded to ingest the test data. The script requires the URL of the ICAT SOAP interface, authentication credentials and the location of the data file to load.

```Shell
icatingest.py --url 'https://localhost:8181/ICATService/ICAT?wsdl' --no-check-certificate --auth simple --user root --pass pw --format YAML --inputfile lils.yml
```

The script can take a while (10-20 minutes!) to load all the data.

Check that ICAT now contains the data
-------------------------------------

We can use the cURL tool to access and query the REST interface of the ICAT Server. First we log in to get a session ID which we can use for subsequent API calls:

```Shell
curl -k --data 'json={"plugin":"simple", "credentials": [{"username":"root"}, {"password": "pw"}]}' https://localhost:8181/icat/session
```
will output something similar to:
```JSON
{"sessionId":"1c7a8fe1-43f2-4fb3-9692-2425a7d36738"}
```


```Shell
curl -k -d 'sessionId=1c7a8fe1-43f2-4fb3-9692-2425a7d36738' --data-urlencode 'query=SELECT f FROM Facility f' -G https://localhost:8181/icat/jpql
```



```Shell
curl -k -d 'sessionId=1c7a8fe1-43f2-4fb3-9692-2425a7d36738' --data-urlencode 'query=SELECT d.description FROM Dataset d' -G https://localhost:8181/icat/jpql
```

```Shell
curl -k -d 'sessionId=1c7a8fe1-43f2-4fb3-9692-2425a7d36738' --data-urlencode 'query=SELECT df.name FROM Datafile df' -G https://localhost:8181/icat/jpql
```
