---
layout: master
title: 在Ubuntu上安装Storm Cluster
---
# {{ page.title }} #

## Steps  ##

### 1. Install ZeroMQ ###
<pre class="brush:bash">
wget http://download.zeromq.org/historic/zeromq-2.1.7.tar.gz
tar -xzf zeromq-2.1.7.tar.gz
cd zeromq-2.1.7
./configure
make
sudo make install
</pre>
### 2. Install JZMQ ###
<pre class="brush:bash">
git clone https://github.com/nathanmarz/jzmq.git
cd jzmq
./autogen.sh
./configure
make
sudo make install
</pre>
### 3. Install Zookeeper cluster ###
<pre class="brush:bash">
wget http://www.eu.apache.org/dist/zookeeper/zookeeper-3.4.5/zookeeper-3.4.5.tar.gz
</pre>
### 4. Config and Start Zookeeper cluster ###
<pre class="brush:bash">
conf/zoo.cfg
tickTime=2000
dataDir=/var/zookeeper
clientPort=2181
bin/zkServer.sh start
bin/zkCli.sh -server 127.0.0.1:2181
</pre>



<p>{{ page.date | date_to_string }}</p>

