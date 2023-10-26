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
# Data Type Supported

| CQL Type  | Constants supported                       |
|-----------|-------------------------------------------|
| ascii     | strings                                   |
| bigint    | integers                                  |
| blob      | blobs                                     |
| boolean   | booleans                                  |
| counter   | integers                                  |
| date      | strings                                   |
| decimal   | integers, floats                          |
| double    | integers, floats                          |
| float     | integers, floats                          |
| frozen    | user-defined types,   collections, tuples |
| inet      | strings                                   |
| int       | integers                                  |
| list      | n/a                                       |
| map       | n/a                                       |
| set       | n/a                                       |
| smallint  | integers                                  |
| text      | strings                                   |
| time      | strings                                   |
| timestamp | integers, strings                         |
| timeuuid  | uuids                                     |
| tinyint   | integers                                  |
| tuple     | n/a                                       |
| uuid      | uuids                                     |
| varchar   | strings                                   |
| varint    | integers                                  |

# Using via Python (Pyspark)
Import Library
```py
from pyspark.sql import SparkSession
from pyspark.sql.types import *
from pyspark.sql.functions import *
import pandas as pd
import pyspark
import os
```
## Spark ENV
* ('spark.jars.packages','com.datastax.spark:spark-cassandra-connector_2.12:3.1.0')

```py
os.environ['JAVA_HOME'] = '/usr/local/jdk8u222-b10'
os.environ['PYSPARK_PYTHON'] ='/HDFS01/miniconda3/envs/pylang39/bin/python'
conf = pyspark.SparkConf().setAll([
     ("spark.cassandra.connection.host", "192.168.10.53"),
     ('spark.jars.packages','com.datastax.spark:spark-cassandra-connector_2.12:3.1.0'),
     ("spark.cassandra.connection.port", "9042"),
     ("spark.cassandra.auth.username", "root"),
     ("spark.cassandra.auth.password", "admin")
    ])
spark = SparkSession.builder \
        .master("local[*]") \
        .appName("testreplace") \
        .config(conf=conf) \
        .enableHiveSupport() \
        .getOrCreate();
```
## Define Schema
```py
schema = StructType([
    StructField("id", IntegerType(), True),
    StructField("name", StringType(), True)
])
```
## Example Data
```py
json = {
    'id': [1,2,3,4,5,6,7],
    'name': ['test1','test2','test3','test4','test5','test6','test7']
}
df_data = pd.DataFrame(json)
```
## Write Data to Cassandra
```py
data_rd = spark.createDataFrame(df_data)
data_rd.write \
    .format("org.apache.spark.sql.cassandra") \
    .options(keyspace="test_db", table="emp") \
    .mode("append") \
    .save()
```
## Read Data
```py
cassandra_df = spark.read \
    .format("org.apache.spark.sql.cassandra") \
    .options(keyspace="test_db", table="flight") \
    .load()
cassandra_df.toPandas()
```
## Alternative Write (Optional)
```py
cassandra_df.write \
    .format("org.apache.spark.sql.cassandra") \
    .options(keyspace="test_db", table="flight") \
    .mode("append") \
    .save()
```
## Stop Spark Worker
```py
spark.stop()
```

> Source [Apache Cassandra](https://cassandra.apache.org/_/index.html)
