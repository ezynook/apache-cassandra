# # Backup And Restore Cassandra
รองรับ
 - [x] Cassandra
 - [x] ScyllaDB

**Backup Keyspaces**

```bash
cqlsh -e "DESC SCHEMA" >  <schema_name.cql>"
```
**Snapshot Data**
```bash
nodetool  snapshot <KEYSPACE_NAME>
```
List to verify `/var/lib/scylla/data/keyspace_name/table_name-UUID/snapshots/snapshot_name`

**Restore Keyspace**
```sql
cqlsh -e "SOURCE '/path_to_schema/<schema_name.cql>'"
```
**Stop services**
```sh
systemctl stop cassandra
```
**Remove Commit Log**
```bash
rm  -rf  /var/lib/scylla/commitlog/*
```
**Delete all file in schema directory**
```bash
rm  -rf  /var/lib/scylla/data/keyspace_name/table_name-UUID/*
```
**Copy Data file to directory**
```bash
cd  /path/to/backup/snapshot
cp  -r  *  /var/lib/scylla/data/keyspace_name/table_name-UUID/
sudo  chown  -R  scylla:scylla  /var/lib/scylla/data/keyspace_name/table_name-UUID
```
**Start services**
```bash
systemctl start cassandra
```
**Confirm Data**

```sh
nodetool  repair
```

**Remove Snapshot**
```sh
nodetool  clearsnapshot <KEYSPACE_NAME>
```
