kafka-docker
============

Dockerfile for [Apache Kafka](http://kafka.apache.org/)

The image is available directly from [DockerHub](https://hub.docker.com/r/dockerkafka/kafka/)

You can browse the source in [GitHub](https://github.com/DockerKafka/kafka-docker)

## Usage

### Pull the image.
```sh
$ docker pull dockerkafka/kafka
```

### Start a server.
```sh
$  docker run -d --name kafkadocker_zookeeper_1  dockerkafka/zookeeper
$  docker run -d --name kafkadocker_kafka_1 --link kafkadocker_zookeeper_1:zookeeper dockerkafka/kafka
```
### Create a topic.
```sh
$  docker run -it --rm --link kafkadocker_zookeeper_1:zookeeper dockerkafka/kafka kafka-topics.sh --create --zookeeper zookeeper:2181 --replication-factor 1 --partitions 5 --topic test
```
###Connect to the server ...

Don't bother the ``` WARN Property topic is not valid (kafka.utils.VerifiableProperties) ``` warn. It is a bug [KAFKA-1711](https://issues.apache.org/jira/browse/KAFKA-1711).

#### ... as a producer.

After connection, you will got a stream. Every line is a new message.

```sh
$  docker run -it --rm --link kafkadocker_kafka_1:kafka dockerkafka/kafka kafka-console-producer.sh --broker-list kafka:9092 --topic test
```
#### ... as a consumer.

After connection, all of the sent messages will be fetched, then wait for new messages.

```sh
$  $ docker run -it --rm --link kafkadocker_zookeeper_1:zookeeper --link kafkadocker_kafka_1:kafka dockerkafka/kafka kafka-console-consumer.sh --zookeeper zookeeper:2181 --topic test --from-beginning
```

## Customization

Check out this repository, you will found the default Kafka configuration files under image/conf.

Start the container with the following line, so now you can modify the config in your host, and then start the server.
```sh
$  docker run -it --rm --volume `pwd`/image/conf:/opt/kafka_2.10-0.8.2.1/config kafkadocker/kafka /bin/bash
```

After you finished the customization & testing of the config changes, build your own image.
```sh
$  docker build --tag="my-image" .
```
