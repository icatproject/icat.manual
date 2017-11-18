Chapter 08: Create Some Test Data
================================

Overview
--------

Usually, data is inputted to ICAT (ingested) from automated scripts running at the scientific facility which is running the instance of ICAT. The scripts extract metadata from the facility booking system, the experimental equipment control systems and the resulting data files and load it into ICAT for cataloging and indexing using one of the APIs - ReST or SOAP - along with the location of the data files. Setting all this up is beyond the scope of this tutorial. Fortunately, there is a script which will create test data - much like the [Lorum Ipsum](https://en.wikipedia.org/wiki/Lorem_ipsum) text generators used by web developers. The script is written for a recent version of the Ruby programming language so we need to install this first.

Install new Ruby version
------------------------

The Ruby version in the tutorial system is too old for the data creation script. Fortunately, we can easily build and install a more up to date version using the Ruby Version Manager ([RVM](https://rvm.io/)). First we must install the dependencies:

```Shell
sudo yum install patch libyaml-devel glibc-headers autoconf gcc-c++ glibc-devel patch readline-devel zlib-devel libffi-devel openssl-devel automake libtool bison sqlite-devel
```
Next we install the Ruby Version Managaer (rvm) and use it to download and install a recent version of Ruby (2.3.1) in userspace. Finally, we use `gem` - the Ruby package manager - to install some dependencies for our test data generator script.
```Shell
curl -sSL https://get.rvm.io | bash
source $HOME/.rvm/scripts/rvm
rvm install 2.3.1
rvm use 2.3.1 --default
gem install faker rest-client
```

Create and load some test data
------------------------------

Download the test data generator script:
```Shell
wget https://raw.githubusercontent.com/icatproject/topcat/master/tools/lorum_facility_generator.rb
```

Change the hard-coded root password in the script: on the line containing the string `password`, find and replace `root` with `pw`. You could do this in your favourite editor or use the `sed` one-liner below. If you use an editor, note that we save the script under a new name: `test_data_generator.rb`.
```Shell
sed -e '/password/ s/root/pw/g' lorum_facility_generator.rb > test_data_generator.rb
```

Run the data creation script
```Shell
ruby test_data_generator.rb
```