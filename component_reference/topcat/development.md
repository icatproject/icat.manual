## Development within a VM

You can create a Topcat development environment via Vagrant \([https://www.vagrantup.com/](https://www.vagrantup.com/)\):

```
git clone https://github.com/icatproject/topcat.git

cd
 topcat
vagrant up
```

Once everything is up and running you'll need to make a security exception, which can be done by going to:

* [https://localhost:8181/topcat/ping](https://localhost:8181/topcat/ping)

You'll then be able to run Topcat by going to:

* [http://localhost:10080/](http://localhost:10080/)

You will be able to log into the "TEST" facility with the following credentials:

* Authentication Type: Simple
* Username: root
* Password: root

You can rebuild the server side \(i.e. anything Java related\) by:

```
vagrant ssh
topcat build_install
```

You can build a distro by:

```
vagrant ssh

cd
 /vagrant
mvn clean install
```

your new build will be in:

* target/

## Native development

If you chose not to use vagrant then you need to setup node and npm yourself. Go to a suitable directory to act as parent to your distribution and:

```
wget https://nodejs.org/dist/v6.11.3/node-v6.11.3-linux-x64.tar.xz
tar --xz -xf node-v6.11.3-linux-x64.tar.xz 
&
&
 rm node-v6.11.3-linux-x64.tar.xz

```

to get what is currently the LTS version of node and npm. Then add to $PATH the directory: node-v6.11.3-linux-x64/bin and

```
npm install -g bower
npm install -g grunt-cli

```

to install bower and grunt-cli "globally". Then go to where you have checked out topcat and do the usual

```
mvn clean install

```



