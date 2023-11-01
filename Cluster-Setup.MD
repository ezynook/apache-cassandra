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
## Install Conda
```bash
cd ~
wget https://repo.anaconda.com/miniconda/Miniconda3-py39_23.9.0-0-Linux-x86_64.sh
chmod +x /Miniconda3-py39_23.9.0-0-Linux-x86_64.sh
bash ~/./Miniconda3-py39_23.9.0-0-Linux-x86_64.sh
/root/miniconda3/bin/conda init
source ~/.bashrc
```
## Add Username
```bash
useradd cassandra
passwd cassandra
#-----------------
#New password Here
#-----------------
```
## Install Apache Cassandra All hosts
```bash
cd /opt/
wget https://dlcdn.apache.org/cassandra/4.1.3/apache-cassandra-4.1.3-bin.tar.gz
tar zxvf apache-cassandra-4.1.3-bin.tar.gz
mv apache-cassandra-4.1.3 cassandra
chmod 777 -R cassandra
chown cassandra:cassandra -R cassandra
vim /etc/profile
#-----------Copy Paste--------------
export CASSANDRA_HOME=/opt/cassandra
export PATH=$PATH:$CASSANDRA_HOME/bin
#-----------------------------------
source  /etc/profile
vim ~/.bashrc
#-------------Copy Paste--------------------
alias nodetool='/opt/cassandra/bin/nodetool'
alias cqlsh='/opt/cassandra/bin/cqlsh --request-timeout=10000'
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
write_request_timeout_in_ms: 60000ms
read_request_timeout_in_ms: 60000ms
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
## Script to Start services
```bash
vim /etc/systemd/system/cassandra.service
#------------Copy Paste-----------------
[Unit]
Description=Apache Cassandra database server
After=network.target

[Service]
Type=forking
User=cassandra
Group=cassandra
ExecStart=/opt/cassandra/bin/cassandra -p /var/run/cassandra/cassandra.pid
ExecStop=/opt/cassandra/bin/kill.sh
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
systemctl daemon-reload
systemctl start cassandra
systemctl status cassandra
```
## Remove Node
```bash
ssh root@master_node
nodetool removenode <host-id>
```
## Error Schema Version failure
```bash
#Remove keyspace
drop keyspace <keyspace_name>;
#Restart service
systemctl stop cassandra
systemctl start cassandra
#Repair host (Main Node Only)
nodetool repair --full
nodetool status
#is Normal
Schema versions:
		2a0133ac-65e3-3a9f-a60d-0763f2a4d625: [192.168.10.53, 192.168.10.22, 192.168.10.54]
#Create new keyspace
CREATE KEYSPACE test_db
  WITH REPLICATION = { 
   'class' : 'SimpleStrategy', 
   'replication_factor' : <Number Of Replication> 
  };
```
## User Permission
Config ```/opt/cassandra/bin/cassandra.yaml```
```yaml
authenticator: PasswordAuthenticator
authorizer: org.apache.cassandra.auth.CassandraAuthorizer
```
Restart services all hosts after edit ```systemctl stop cassandra``` | ```systemctl start cassandra```

Create and Grant Users ```cqlsh --request-timeout=10000 -u cassandra -p cassandra```
```sql
CREATE USER <some_user> WITH PASSWORD 'password';
USE <keyspace>;
GRANT SELECT ON <table_name> TO <some_user>;
LIST ALL PERMISSIONS OF nook;
```
Grant Privillage

* ALL PERMISSIONS
* ALTER
* AUTHORIZE
* CREATE
* DESCRIBE
* DROP
* EXECUTE
* MODIFY
* SELECT

source_name

* ALL FUNCTIONS
* ALL FUNCTIONS IN KEYSPACE keyspace_name
* FUNCTION function_name
* ALL KEYSPACES
* KEYSPACE keyspace_name
* TABLE table_name
* ALL MBEANS
* MBEAN mbean_name
* MBEANS pattern
* ALL ROLES
* ROLE role_name

```sql
GRANT priviledge
ON resource_name
TO role_name
```

> Author: Pasit Y. | Last Writed 20231030
