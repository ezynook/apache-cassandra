<div align="center">
    <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/5/5e/Cassandra_logo.svg/1200px-Cassandra_logo.svg.png" width="150">
    <h1>Apache Cassandra</h1>
</div>

# Setup

```bash
$ cd ~
$ git clone https://github.com/ezynook/apache-cassandra.git
$ docker-compose up -d
```
# One-line Deploy
```bash
$ docker volume create cassandra_data
$ docker run \
       --name cassandra \
       -p 7000:7000 \
       --restart=unless-stopped \
       -v cassandra_data:/opt/cassandra/data \
       -d ghcr.io/ezynook/cassandra/cassandra:latest
```
# Check services
```bash
CONTAINER ID   IMAGE              COMMAND       CREATED             STATUS        PORTS                    NAMES
f0cc270e1410   ghcr.io/ezynook...  "pasitdev"   1 seconds   1 seconds (healthy)   0.0.0.0:7001->7000/tcp   cassandra
```
[Link CQLSH Command](https://docs.datastax.com/en/cql-oss/3.3/cql/cql_reference/cqlReferenceTOC.html)
# CLI Command

```docker exec -it cassandra bash```
# Create Keyspace
Create a keyspace for a single node evaluation cluster
```sql
CREATE KEYSPACE test_db
  WITH REPLICATION = { 
   'class' : 'SimpleStrategy', 
   'replication_factor' : <Number Of Replication> 
  };
```
Create a keyspace NetworkTopologyStrategy on an evaluation cluster
```sql
CREATE KEYSPACE test_db 
  WITH REPLICATION = { 
   'class' : 'NetworkTopologyStrategy', 
   'datacenter1' : 1 
  } ;
```

---

# Create Table
```sql
CREATE TABLE test_db.emp (
   id int, 
   name text
   PRIMARY KEY (name, id)) 
WITH CLUSTERING ORDER BY (id ASC);
```
# Insert Data

**สามารถใช้งาน SQL Insert, Update, Delete ได้ตามปกติ**

Insert CSV to Table
```sql
COPY keyspace.table FROM '~/testcsv.csv' WITH DELIMITER='|' AND HEADER=TRUE
```
Insert JSON Table
```sql
INSERT INTO test_db.emp JSON '{
  "category" : "GC", 
  "points" : 780, 
  "id" : "829aa84a-4bba-411f-a4fb-38167a987cda",
  "lastname" : "SUTHERLAND" }';
```


> Source [Apache Cassandra](https://cassandra.apache.org/_/index.html)
