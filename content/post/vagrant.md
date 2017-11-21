---
title: "Vagrant"
date: 2017-11-21T10:12:07+01:00
tags: [ "vagrant", "virtual box", "dev"]
---

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


