---
title: "Install jupyter with spark"
date: 2018-02-05T11:35:09+01:00
tags: [ "vagrant", "jupyter", "spark", "dlab" ]
author: "mattische"
---

<pre>
For Spark installation in a vagrant instance, see previous <a href="https://mattische.github.io/blog/post/spark_installation_vagrant/">post</a>.
</pre>


### Prerequisites
You should have a working Spark installation inside a vagrant machine (Ubuntu Xenial 16.04).
See previous <a href="https://mattische.github.io/blog/post/spark_installation_vagrant/">post</a> for doing that.

<br><br><br>

### Provision vagrant - expose ports
In your folder, where your vagrant-machine is located; edit your <pre>Vagrantfile</pre>

We will expose the ports for Jupyter and Spark to the host (you physical machine that runs vagrant).

We will expose 2 ports - 8888 for the Jupyter notebooks and 7077 for the Spark master.
Add two entries for each port, via <pre>config.vm.network </pre> 

Vagrantfile;

<code class="bash">
Vagrant.configure("2") do |config|

     config.vm.network "forwarded_port", guest: 8888, host: 8888

     config.vm.network "forwarded_port", guest: 7077, host: 7077
</code>

After that you'll have to reload the configuration. This can be done via;

<code class="bash">
$ vagrant reload

#or/and

$ vagrant provision
</code>

Then run and enter your vagrant machine.
<br><br><br>



### Install pip and Jupyter
Inside our vagrant machine, we are going to install <a href="http://jupyter.org/">Jupyter</a> via pip.

First, lets install pip, if we have'nt done so already ;

<code class="bash">
$ sudo apt-get install python-pip python-dev build-essential 

$ sudo pip install --upgrade pip 
</code>

Then we install jupyter via pip;

<code class="bash">
$ sudo pip install jupyter
</code>
<br><br><br>


### Set the environment vars
As in previous post we need to have the correct env vars set.

For convenience, here is our .bashrc;

<code class="bash">
export JAVA_HOME=‘/opt/jdk’

export SPARK_HOME=/home/vagrant/spark

export PATH=$SPARK_HOME/bin:$PATH

export PYSPARK_PYTHON=python3

export PYTHONPATH=$SPARK_HOME/python:$SPARK_HOME/python/lib/py4j-0.10.3-src.zip:$PYTHONPATH
</code>
<br><br><br>



### Create Jupyter start-script
Place a .sh file in your home dir. 
I named it <pre>jupyter.sh</pre>

DO NOT SPLIT THIS INTO SEVERAL LINES;

<code class="bash">
#!/bin/sh

SPARK_HOME="" HADOOP_HOME="" YARN_HOME="" SPARK_JAR="" HADOOP_COMMON_LIB_NATIVE_DIR="" HADOOP_HDFS_HOME="" HADOOP_COMMON_HOME="" HADOOP_OPTS="" YARN_CONF_DIR="" HADOOP_MAPRED_HOME="" PYSPARK_DRIVER_PYTHON="jupyter" PYSPARK_DRIVER_PYTHON_OPTS="notebook --allow-root --no-browser --port 8888" /root/spark/bin/pyspark --master spark://$SPARK_MASTER_HOST:7077 --driver-memory 2g
</code>


You'll notice the $SPARK_MASTER_HOST var. I set it to localhost.


Make sure it is executable;
<code class="bash">
$ chmod a+x jupyter.sh
</code>
<br><br><br>

### Make sure Spark is running
<code class="bash">
$ sudo ./spark/sbin/start-master.sh
</code>

The above runs spark on the master - since we have only one instance this is the master.
<br><br><br>



### Run jupyter
Now, we may run the jupyter-notebook server with the script;

<code class="bash">
$ sudo ./jupyter.sh
</code>

That command will present the URL-to our notebook-server, with a token at the end. 

Since we exposed port 8888 on our host, we can now open <a href="http://localhost:8888">localhost:8888</a> in our web browser.

Copy the token from the URL in the terminal, and login via localhost:8888, using the token.

You should see something like this when logged in;

<img src="https://raw.githubusercontent.com/mattische/blog/master/static/img/jupyter.PNG" height="50%" width="50%" />
