---
title: "Vagrant"
date: 2017-11-21T10:12:07+01:00
tags: [ "vagrant", "virtual-box", "dev"]
author: "mattische"
---

<img src="https://images.g2crowd.com/uploads/product/image/social_landscape/social_landscape_1489710078/vagrant.png" />

## Installing vagrant


Vagrant is a pretty nifty tool in the sense that have your own virtual development env on your machine.
For example, you can simulate your production env.

You'll need some basic linux knowledge.

For windows;<br />
Download and install vagrant and virtualbox;<br />
https://www.vagrantup.com/<br />
https://www.virtualbox.org/

It's a good idea to create a vagrant account. With it, you can save your own boxes/configurations. Go to https://app.vagrantup.com/ and create an account.


After installation the <pre>$ vagrant</pre> 

command should be accessible.
I recommend using Gitbash for all terminal work on windows (https://git-scm.com/downloads). Cmd just does not cut it.

After installation it is pretty straightforward - you'll get terminal acces to a virtual linux box of your choice.
Choose a folder on your machine where you'll work. In a terminal you run a set of commands that will download, initiate, run and ssh in to that certain box. And voila, you are ready to work.

For example, if you would like to work with ubuntu xenial 64bit, you would do;

<pre><code>
$ vagrant init ubuntu/xenial64
$ vagrant up
$ vagrant ssh
</code></pre>

the commands downloads, boots and you will access the virtual box terminal via ssh.


Go to https://app.vagrantup.com/ to search and find different boxes and read the documentation.


## Provisioning


Provisioning in vagrant is efficient when  you need to run several boxes with same software and configuration.
It's either that or save you configured box at vagrantup.

In the directory you wish to run your box you can place a <pre>Vagrantfile</pre> 

which contains the configuration.

Here is an (stripped down) example;

<pre>
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"
  config.vm.network "forwarded_port", guest: 3306, host: 3333
  config.vm.network "forwarded_port", guest: 5000, host: 8080
  config.vm.synced_folder ".", "/vagrant"
  config.vm.provision "shell", path: "setup.sh" #inline: <<-SHELL
end
</pre>

It's ruby indeed.

- The first line, config.vm.box, tells what OS and version that should be used.

- The second and third lines, forwards the box 3306 port to the host (your actual machine) 3333 port.
We want to expose MySql database to the host and use it from the host (and possibly from outside the host as well).

- The fourth line, syncs the folder <pre>/vagrant</pre> 
on the box to the folder (.) where the box is run in on the host.
This is handy since we can use IDE's and editors for source code on the host.

- The last line links a shell-script file with a set of commands that runs and installs some software when we provision the box.
This way we don't need to run the commands for the same software everytime we reboot or do anything that changes the box state.


Here is an example of at sh-script, setup.sh, that installs software and configures MySql server;

<pre>
# Change these to suit your needs...
DBHOST=localhost
DBNAME=dbname
DBUSER=dbuser
DBPASSWD=test123
DB_ROOT_PASS=password123

apt-get -y install curl build-essential python-software-properties git python3

debconf-set-selections <<< "mysql-server mysql-server/root_password password $DB_ROOT_PASS"
debconf-set-selections <<< "mysql-server mysql-server/root_password_again password $DB_ROOT_PASS"

apt-get -y install mysql-server

# mysql -uroot -p$DB_ROOT_PASS -e "CREATE DATABASE $DBNAME"
mysql -uroot -p$DB_ROOT_PASS -e "grant all privileges on $DBNAME.* to '$DBUSER'@'$DBHOST' identified by '$DBPASSWD'" 
</pre>
