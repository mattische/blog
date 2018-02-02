---
title: "Spark installation in vagrant"
date: 2018-02-02T13:45:29+01:00
tags: [ "vagrant", "spark", "dlab" ]
author: "mattische"
---



<pre>
Reference: <a href="http://dmml.nu/spark-install]">thank you Bin</a>
</pre>

### Ubuntu xenial
This was done inside a vagrant machine; ubuntu xenial (16.04).

<code>
$ vagrant init envimation/ubuntu-xenial \
$ vagrant up \
$ vagrant ssh
</code>

and you're in.


### Install openjdk

<code class="bash">
sudo apt-get update && \
sudo apt-get install -y openjdk-8-jdk && \
ll /usr/lib/jvm/ && \
ll /usr/bin/java && \
ll /etc/alternatives/java && \
sudo update-alternatives --config java
</code>


<code class="bash">
sudo ln -s /usr/lib/jvm/java-8-openjdk-amd64 /opt/jdk
export JAVA_HOME='/opt/jdk' && \
echo $JAVA_HOME
</code>

You might consider putting the vars ($JAVA_HOME) in bash_rc or similar.


### Install spark

<code class="bash">
wget http://www.apache.org/dist/spark/spark-2.0.2/spark-2.0.2-bin-hadoop2.7.tgz && \
tar xvzf spark-2.0.2-bin-hadoop2.7.tgz && \
mv spark-2.0.2-bin-hadoop2.7 spark && \
export SPARK_HOME=/home/vagrant/spark && \
export PATH=$SPARK_HOME/bin:$PATH && \
echo $PATH
</code>


When creating and exporting the $SPARK_HOME, vagrant is assumed as the user.


In case you want/need python 3 - install that as well (sudo apt-get install python3)

<code class="bash">
export PYSPARK_PYTHON=python3
</code>


### test installation
Python shell; <code>$ pyspark</code> and a Scala shell: <code>pyspark-shell</code>

You should see something like;

<img src="/blog/static/img/spark.png" />

