---
title: "Spark cluster how-to"
date: 2018-02-12T11:40:21+01:00
tags: [ "cluster", "spark", "dlab" ]
author: "mattische"
---

<pre>
This is how to get a Spark cluster running, with one master and one slave (worker).
<br>
I used a droplet on digital ocean (master) and one local vagrant machine (slave).<br>
<a href="https://mattische.github.io/blog/post/spark_installation_vagrant/">spark in vagrant</a>
<br>


References:

<a href="http://dmml.nu/spark-install">thank you Bin</a><br>
<a href="http://paxcel.net/blog/how-to-setup-apache-spark-standalone-cluster-on-multiple-machine/">kulwant singh</a><br>
<a href="https://blog.insightdatascience.com/spinning-up-a-spark-cluster-on-spot-instances-step-by-step-e8ed14ebb3b">austin ouyang</a>


</pre>

### Installation on all nodes (master and slaves)

For the slave (worker) I used a vagrant machine as in my <a href="https://mattische.github.io/blog/post/spark_installation_vagrant/">previous post</a>.<br>
For the master I used a droplet on Digital Ocean.<br><br>

_Installation on both master and slaves;_<br><br>

Install java;<br>
<code class="bash">
$ sudo apt-get update && \
sudo apt-get install -y openjdk-8-jdk && \
ll /usr/lib/jvm/ && \
ll /usr/bin/java && \
ll /etc/alternatives/java && \
sudo update-alternatives --config java
</code>
<br>Environment var for Java;<br>
<code class="bash">
$ sudo ln -s /usr/lib/jvm/java-8-openjdk-amd64 /opt/jdk
$ export JAVA_HOME=/opt/jdk && \
echo $JAVA_HOME
</code>

<br>
<br>

Install spark;<br>
<code class="bash">
$ wget http://www.apache.org/dist/spark/spark-2.0.2/spark-2.0.2-bin-hadoop2.7.tgz && \
tar xvzf spark-2.0.2-bin-hadoop2.7.tgz && \
mv spark-2.0.2-bin-hadoop2.7 spark && \
export SPARK_HOME=/home/vagrant/spark && \
export PATH=$SPARK_HOME/bin:$PATH && \
echo $PATH
</code>



If you don't have python 3 - install that as well (along with packages for jupyter (which you'll most likely want)).<br>
Also, install scala;<br>
<pre>
$ sudo apt-get install python-dev python-pip python-numpy python-scipy python-pandas gfortran python3 scala
</pre>
<br>



<code class="bash">
$ export PYSPARK_PYTHON=python3
</code>


<br>
Now we can access both shells; _$ pyspark_(python) and _$ spark-shell_ (scala).

<br><br>

### Conf and start master

The configuration added is located in <pre>SPARK_HOME/conf/spark-env.sh</pre><br>


There is a template-configuration file which you can copy and save as spark-env.sh.<br>
In spar-env.sh add we add our master IP;


<pre>
SPARK_MASTER_HOST=IP_ADDRESS
</pre>


Start the master with;<br>

<pre>$ spark/sbin/start-master.sh</pre>




### Start the slave (worker)


When starting the slave, we supply the IP of the master like so;<br>

<pre>$ spark/sbin/start-slave.sh spark://SPARK_MASTER_IP:7077</pre>

<br>
<br>
The output in the log, if you tail -f it, should be similar to 

<pre>INFO Worker: Successfully registered with master spark://SPARK_MASTER_IP:7077</pre>




Now we can acces the master web view via port 8080.<br>
There, it should output that one worker is active.<br>

<img src="https://raw.githubusercontent.com/mattische/blog/master/static/img/spark-master.PNG" />
