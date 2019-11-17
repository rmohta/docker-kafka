Kafka in Docker
===

-----
Forked and upgraded version of https://github.com/spotify/docker-kafka

-----

This repository provides everything you need to run Kafka in Docker.

For convenience also contains a packaged proxy that can be used to get data from
a legacy Kafka 7 cluster into a dockerized Kafka 8.

Why?
---
The main hurdle of running Kafka in Docker is that it depends on Zookeeper.
Compared to other Kafka docker images, this one runs both Zookeeper and Kafka
in the same container. This means:

* No dependency on an external Zookeeper host, or linking to another container
* Zookeeper and Kafka are configured to work together out of the box

Run
---

```bash
docker run -p 2181:2181 -p 9092:9092 --env KAFKA_LISTENERS="PLAINTEXT://:9092" rmohta/docker-kafka:latest
```

```bash
export KAFKA=localhost:9092
kafka-console-producer.sh --broker-list $KAFKA --topic test
```

```bash
export KAFKA=localhost:9092
kafka-console-consumer.sh --broker-list $KAFKA --topic test
```

Running the proxy
-----------------

Take the same parameters as the spotify/kafka image with some new ones:
 * `CONSUMER_THREADS` - the number of threads to consume the source kafka 7 with
 * `TOPICS` - whitelist of topics to mirror
 * `ZK_CONNECT` - the zookeeper connect string of the source kafka 7
 * `GROUP_ID` - the group.id to use when consuming from kafka 7

```bash
docker run -p 2181:2181 -p 9092:9092 \
    --env ADVERTISED_HOST=`boot2docker ip` \
    --env ADVERTISED_PORT=9092 \
    --env CONSUMER_THREADS=1 \
    --env TOPICS=my-topic,some-other-topic \
    --env ZK_CONNECT=kafka7zookeeper:2181/root/path \
    --env GROUP_ID=mymirror \
    spotify/kafkaproxy
```

In the box
---
* **spotify/kafka**

  The docker image with both Kafka and Zookeeper. Built from the `kafka`
  directory.

* **spotify/kafkaproxy**

  The docker image with Kafka, Zookeeper and a Kafka 7 proxy that can be
  configured with a set of topics to mirror.

Public Builds
---

https://hub.docker.com/repository/docker/rmohta/docker-kafka


Build from Source
---

    docker build -t spotify/kafka kafka/
    docker build -t spotify/kafkaproxy kafkaproxy/

Todo
---

* Not particularily optimzed for startup time.
* Better docs

