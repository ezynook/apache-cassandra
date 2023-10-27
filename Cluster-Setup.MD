# Install Apache Cassandra Cluster
## SSH Copy Key (All hosts)
```bash
cat > /etc/hosts <<EOF
192.168.10.53	cassandra1
192.168.10.54	cassandra2
127.0.0.1   localhost
EOF
ssh-keygen
ssh-copy-id root@cluster_ip
ssh-copy-id root@cluster_hostname
```
## Install OpenJDK
```bash
wget https://github.com/AdoptOpenJDK/openjdk8-binaries/releases/download/jdk8u222-b10/OpenJDK8U-jdk_x64_linux_hotspot_8u222b10.tar.gz
tar -zxvf OpenJDK8U-jdk_x64_linux_hotspot_8u222b10.tar.gz
chown -R root:root jdk8u222-b10/
chmod -R 755 jdk8u222-b10/
mv jdk8u222-b10/ /usr/local/
vi /etc/profile
#--------------Copy Paste---------------
export JAVA_HOME=/usr/local/jdk8u222-b10
export PATH=$JAVA_HOME/bin:$PATH
#---------------------------------------
source  /etc/profile
```
# Install Conda
```bash
cd ~
wget https://repo.anaconda.com/miniconda/Miniconda3-py39_23.9.0-0-Linux-x86_64.sh
chmod +x /Miniconda3-py39_23.9.0-0-Linux-x86_64.sh
bash ~/./Miniconda3-py39_23.9.0-0-Linux-x86_64.sh
/root/miniconda3/bin/conda init
source ~/.bashrc
```
# Install Apache Cassandra All hosts
```bash
cd /opt/
wget https://dlcdn.apache.org/cassandra/4.1.3/apache-cassandra-4.1.3-bin.tar.gz
tar zxvf apache-cassandra-4.1.3-bin.tar.gz
mv apache-cassandra-4.1.3 cassandra
vim /etc/profile
#-----------Copy Paste--------------
export CASSANDRA_HOME=/opt/cassandra
export PATH=$PATH:$CASSANDRA_HOME/bin
#-----------------------------------
source  /etc/profile
vim ~/.bashrc
#-------------Copy Paste--------------------
alias nodetool='/opt/cassandra/bin/nodetool'
alias cqlsh='/opt/cassandra/bin/cqlsh'
#-------------------------------------------
source ~/.bashrc
```
# Setting up Cassandra Multi-node Cluster
```bash
pip install cassandra-driver
vim /opt/cassandra/conf/cassandra.yaml
```
```yaml
cluster_name: 'Softnix-Cluster'

seed_provider:
  - class_name: org.apache.cassandra.locator.SimpleSeedProvider
    parameters:
         - seeds: "192.168.10.53, 192.168.10.54"
#ใส่เป็น IP เครื่องตัวเอง, เครื่องอื่นๆก็ใส่เป็น IP เครื่องตัวเอง
listen_address: 192.168.10.53
rpc_address: 192.168.10.53
endpoint_snitch: GossipingPropertyFileSnitch
auto_bootstrap: true
```
## Modify Rack Details
```bash
vim /opt/cassandra/conf/cassandra-rackdc.properties
#----Edit Here------
dc=datacenter1
rc=rack1
#-------------------
```
```bash
rm -rf /opt/cassandra/data/*
```
# Script to Start services
```bash
vim /opt/cassandra/bin/cassandra.sh
#--------------Copy Paste--------------
/opt/cassandra/bin/cassandra -R
#--------------------------------------
chmod +x /opt/cassandra/bin/cassandra.sh
vim /etc/systemd/system/cassandra.service
#------------Copy Paste-----------------
[Unit]
Description=Apache Cassandra server
After=network.target

[Service]
Type=forking
User=cassandra
Group=cassandra
ExecStart=/opt/cassandra/bin/cassandra.sh
ExecStop=/opt/cassandra/bin/nodetool stop
Restart=on-failure

[Install]
WantedBy=multi-user.target
#----------------------------------------
```
## Start service (All hosts)
```bash
systemctl daemon-reload
systemctl start cassandra
systemctl status cassandra
```
## Dead Node Fix
```bash
vim /opt/cassandra/conf/cassandra-env.sh
#เพิ่มคำสั่งนี้บรรทัดสุดท้าย
JVM_OPTS="$JVM_OPTS -Dcassandra.replace_address=<Your Host IP Address>"
```
## Create Kill proccess script
Change path to Cassandra
```bash
cd /opt/cassandra/bin/kill.sh
```
Copy code and paste in ```kill.sh```
```bash
#!/bin/bash

APPNAME="cassandra"
pkill $APPNAME

for ps in $(ps aux | grep $APPNAME | awk '{print $2}'); do
        kill -9 $ps > dev/null 2>&1
        echo "Kill $ps Successfully"
done
```
Change execute permission
```bash
chmod +x /opt/cassandra/bin/kill.sh
```
How to use
```bash
$ /opt/cassandra/bin/kill.sh
```
## How to add nodes
* ```ติดตั้ง Java ตาม Step ด้านบนให้เรียบร้อย```
Rsync Cassandra จากเครื่องก่อนหน้า
```bash
ssh root@192.168.10.53 #Master IP
rsync -avh --progress /opt/cassandra root@ip_address_host_to_join:/opt/
```
Config ```vim /opt/cassandra/conf/cassandra.yaml``` ตามด้านล่างนี้
```yaml
seed_provider:
  - class_name: org.apache.cassandra.locator.SimpleSeedProvider
    parameters:
      # Ex: "<ip1>,<ip2>,<ip3>"
      - seeds: "<IP ที่ต้องการเพิ่ม>,192.168.10.53,192.168.10.54"

listen_address: <IP ที่ต้องการเพิ่ม>
rpc_address: <IP ที่ต้องการเพิ่ม>
```
Starting service
```bash
/opt/cassandra/bin/cassandra -R
```
## Remove Node
```bash
ssh root@master_node
nodetool removenode <host-id>
```

> Author: Pasit Y. | Last Writed 20231026
