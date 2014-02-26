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
### 4. Install Storm ###
<pre class="brush:bash">
wget https://github.com/downloads/nathanmarz/storm/storm-0.8.1.zip
conf/storm.yaml
storm.zookeeper.servers:
  - "111.222.333.444"
  - "555.666.777.888"
storm.local.dir: "/mnt/storm"
nimbus.host: "111.222.333.44"
supervisor.slots.ports:
    - 6700
    - 6701
    - 6702
    - 6703  
</pre>
### 5. Launch Nimbus and Supervisor ###

1.Nimbus: Run the command "bin/storm nimbus" under supervision on the master machine.
2.Supervisor: Run the command "bin/storm supervisor" under supervision on each worker machine. The supervisor daemon is responsible for starting and stopping worker processes on that machine.
3.UI: Run the Storm UI (a site you can access from the browser that gives diagnostics on the cluster and topologies) by running the command "bin/storm ui" under supervision. The UI can be accessed by navigating your web browser to http://{nimbus host}:8080.





[installation guide](https://github.com/nathanmarz/storm/wiki/Setting-up-a-Storm-cluster)

<p>{{ page.date | date_to_string }}</p>

