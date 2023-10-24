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
> Source [Apache Cassandra](https://cassandra.apache.org/_/index.html)