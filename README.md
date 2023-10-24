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

# CLI Command

```docker exec -it cassandra bash```

CREATE KEYSPACE
```sql
CREATE KEYSPACE “KeySpace Name”
WITH replication = {'class': ‘Strategy name’, 'replication_factor' : ‘No.Of   replicas’};
```
ALTER KEYSPACE
```sql
ALTER KEYSPACE “KeySpace Name”
WITH replication = {'class': ‘Strategy name’, 'replication_factor' : ‘No.Of  replicas’};
```
DROP KEYSPACE
```sql
DROP KEYSPACE “KeySpace name”
```
CREATE TABLE
```sql
USE test_db;
CREATE TABLE emp(
   emp_id int PRIMARY KEY,
   emp_name text,
   emp_city text,
   emp_sal varint,
   emp_phone varint
);
```
ALTER TABLE
```sql
ALTER TABLE emp ADD emp_email text;
```
DROP TBALE
```sql
DROP TABLE emp;
```
CREATE INDEX
```sql
CREATE INDEX name ON emp1 (emp_name);
```
INSERT DATA
```sql
INSERT INTO emp (emp_id, emp_name, emp_city,
   emp_phone, emp_sal) VALUES(1,'ram', 'Hyderabad', 9848022338, 50000);
```
UPDATE DATA
```sql
 UPDATE emp SET emp_city='Delhi',emp_sal=50000
   WHERE emp_id=2;
```
READ DATA
```sql
select * from emp;
```
DELETE DATA
```sql
DELETE FROM emp WHERE emp_id=3;
```
CSV TO ..
```sql
COPY keyspace.table FROM 'cyclist_category.csv' WITH DELIMITER='|' AND HEADER=TRUE
```
INSERT JSON
```sql
INSERT INTO cycling.cyclist_category JSON '{
  "category" : "GC", 
  "points" : 780, 
  "id" : "829aa84a-4bba-411f-a4fb-38167a987cda",
  "lastname" : "SUTHERLAND" }';
```
DATATYPE
```bash
Data Type	Constants	Description
ascii	strings	Represents ASCII character string
bigint	bigint	Represents 64-bit signed long
blob	blobs	Represents arbitrary bytes
Boolean	booleans	Represents true or false
counter	integers	Represents counter column
decimal	integers, floats	Represents variable-precision decimal
double	integers	Represents 64-bit IEEE-754 floating point
float	integers, floats	Represents 32-bit IEEE-754 floating point
inet	strings	Represents an IP address, IPv4 or IPv6
int	integers	Represents 32-bit signed int
text	strings	Represents UTF8 encoded string
timestamp	integers, strings	Represents a timestamp
timeuuid	uuids	Represents type 1 UUID
uuid	uuids	Represents type 1 or type 4
UUID
varchar	strings	Represents uTF8 encoded string
varint	integers	Represents arbitrary-precision integer
```
> Source [Apache Cassandra](https://cassandra.apache.org/_/index.html)