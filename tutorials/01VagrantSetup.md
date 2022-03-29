| Component   | Version   | Installation                                                        | Documentation                                                               |
| ---------   | -------   | ------------                                                        | -------------                                                               |
| VirtualBox  | 5.2.12    | [Installation Guide](https://www.virtualbox.org/manual/ch02.html)   | [Official Documentation](https://www.virtualbox.org/manual/UserManual.html) |
| Vagrant     | 2.1.2     | [Installation Guide](https://www.vagrantup.com/docs/installation/)  | [Official Documentation](https://www.vagrantup.com/docs/index.html)         |

Chapter 01: Vagrant Setup (Optional)
===================================

This optional chapter describes how to create a virtual machine for the installation on a desktop machine running Windows, OSX or GNU/Linux. It exactly matches the one used to test this tutorial.

Install VirtualBox and Vagrant
------------------------------

1. Download VirtualBox from [here](https://virtualbox.org "VirtualBox Website") and install it.
2. Download Vagrant from [here](https://www.vagrantup.com/downloads.html "Vagrant Download Page") and install it.
3. Create a directory for the virtual machine configuration files.
4. Open a command prompt/terminal and navigate to the directory you created above.
5. Run the following command to initialise your VM:

```Shell
vagrant init centos/7
```
This will create a Vagrant configuration file called `Vagrantfile`. Open the file in an editor. Between the `Vagrant.configure...` line and `end`, add the line:

```Ruby
config.vm.network "forwarded_port", guest: 8080, host: 18080
config.vm.network "forwarded_port", guest: 8181, host: 18181
config.vm.network "forwarded_port", guest: 4848, host: 14848

  config.vm.provider "virtualbox" do |vb|
    # Display the VirtualBox GUI when booting the machine
    vb.gui = false
    # Customize the amount of memory on the VM:
    vb.memory = "2048"
    vb.cpus = 2
  end
```

This setting will allow you to access the TopCat web interface at the end of this tutorial.

Now you can start your VM and log in:
```Shell
vagrant up
vagrant ssh
```

You will now be connected to an `ssh` session on your virtual machine.

*Warning: Avoid issuing a `yum update` as it will install a new kernel which is not able to mount the directory shared between the host and virtual machines.*

For a more detailed introduction to Vagrant, you could follow the tutorial on the Vagrant website [here](https://www.vagrantup.com/intro/getting-started/index.html "Vagrant Getting Started").
