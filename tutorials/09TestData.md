Chapter 09: Ingest Some Test Data
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
**Note: Make sure you install `suds-jurko` and not `suds`. `suds` is no longer maintained.**

Download and install python-icat
--------------------------------

As before, download and uncompress the package:
```Shell
cd ~/downloads
curl -O 'https://icatproject.org/misc/python-icat/download/python-icat-0.14.1.tar.gz'
cd ~/install
tar -xvzf ../downloads/python-icat-0.14.1.tar.gz
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
icatingest.py --url 'https://localhost:8181/ICATService/ICAT?wsdl' --no-check-certificate --auth simple --user root --pass rootpw --format YAML --inputfile lils.yml
```

The script can take a while (10-20 minutes!) to load all the data.

Check that ICAT now contains the data
-------------------------------------

We can use the cURL tool to access and query the [REST interface](https://repo.icatproject.org/site/icat/server/4.9.1/miredot/index.html#home) of the ICAT Server. First we log in to get a session ID which we can use for subsequent API calls. To do this, we POST our credentials to [session](https://repo.icatproject.org/site/icat/server/4.9.1/miredot/index.html#1024708709) endpoint.

```Shell
curl -k --data 'json={"plugin":"simple", "credentials": [{"username":"root"}, {"password": "rootpw"}]}' -w'\n' https://localhost:8181/icat/session
```
will output something similar to:
```JSON
{"sessionId":"1c7a8fe1-43f2-4fb3-9692-2425a7d36738"}
```
The `-w'\n'` option appends a newline character to the output to make it easier to read.
**Note: We use a form parameter 'json=' not JSON in the body of the request as with many REST APIs.**

Let's store the `sessionId` in an environment variable to make the future queries easier to type.  You will need to substitute your `sessionId` in the command below:
```Shell
export ICAT_SESSION=1c7a8fe1-43f2-4fb3-9692-2425a7d36738
```

Once we have a `sessionId`, we can use it to send queries to the [jpql](https://repo.icatproject.org/site/icat/server/4.9.1/miredot/index.html#-1766420604) endpoint. [JPQL](https://docs.oracle.com/javaee/7/tutorial/persistence-querylanguage.htm) - Java Persistence Query Language - is a query language similar to SQL which can be used to query the ICAT database. We need to use cURL's `--data-urlencode` option to correctly encode spaces in our query.

```Shell
curl -k --data "sessionId=$ICAT_SESSION" --data-urlencode 'query=SELECT f.name FROM Facility f' -w'\n' --get https://localhost:8181/icat/jpql
```
will output:
```Shell
Count 1: LILS
```
`LILS` is the facility name (short for "Lorum Ipsum Light Source") in our randomly-generated data.

As well as the `sessionId` and `query` parameters, the `jpql` endpoint accepts a `max` parameter to set the maximum number of results to return. It defaults to 5. Let's try it out with a more complex query:

```Shell
curl -k --data "sessionId=$ICAT_SESSION" --data-urlencode 'query=SELECT df.name, df.location, df.fileSize FROM Datafile df where df.fileSize < 1000' --data "max=10" -w'\n' --get https://localhost:8181/icat/jpql
```
will output something like:
```Shell
Count at least 5: [Datafile 20, dolore/ut/deserunt, 802], [Datafile 53, dolorem/a/accusamus, 815], [Datafile 19, atque/dignissimos/odit, 422], [Datafile 55, sunt/autem/laudantium, 407], [Datafile 44, ea/sit/nostrum, 59], [Datafile 87, distinctio/doloremque/provident, 838], [Datafile 34, voluptas/perspiciatis/in, 677], [Datafile 15, vero/soluta/eius, 457], [Datafile 58, vel/soluta/veritatis, 68], [Datafile 51, aut/aperiam/itaque, 900]

```

You can find the ICAT Schema reference [here](https://repo.icatproject.org/site/icat/server/4.9.1/schema.html) and experiment with some other queries. **Note: not all the schema entities and fields have data in this example.**
