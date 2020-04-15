# 1. Installation and Testing

In this task we asked to install Apache Kafka and Run as Docker Container. We also asked to Test Apache Kafka using ```Conduktor```.

## Installation

### Create Docker Network

Fist we need to create local netwok on docker so each container inside docker network can communicate each other. In this case Kafka is required to have connection to Zookeeper which both in different container.

![Create Docker Network](https://github.com/wildangbudhi/Stream-Processing-using-Apache-Kafka/blob/master/1.%20Installation%20and%20Testing/Screenshoot/1.%20Create%20Docker%20Network.png)

We can use command below:

```bash
$ docker network create <docker_network_name> --driver <driver_mode>
```

For the ```docker_network_name``` we can define up to you. But here i'll make the name as ```kafka-net```. And the ```driver_mode``` this case we choose ```bridge``` because it only needed for communication between Zookeeper Container and Kafka Container.

```bash
$ docker network create kafka-net --driver bridge
```

### Create and Run Apache Zookeeper Container

Kafka is Required Apache Zookeeper as coordinator of Kafka. Kafka stores information about the cluster and consumers into Zookeeper.

![Create and Run Apache Zookeeper Container](https://github.com/wildangbudhi/Stream-Processing-using-Apache-Kafka/blob/master/1.%20Installation%20and%20Testing/Screenshoot/2.%20Pull%20Image%2C%20Create%20Container%2C%20and%20Run%20Zookepeer.png)

We can use command below:

```bash
$ docker run --name zookeeper-server -d -p 2181:2181 --network kafka-net -e ALLOW_ANONYMOUS_LOGIN=yes bitnami/zookeeper:latest

```

When we want to create and run Docker Container we need to have an image for the container. But ```docker run``` command automaticlly check wheter the image needed is available in local repository or not. If its not available in local repository the automaticlly pull required image from Docker Hub.

Parameters:
- ```--name zookeeper-server``` : is the name of container
- ```-d``` : For run the container on Deamon mode or background process
- ```-p 2181:2181``` : this is for Binding local docker network port 2181 to local port 2181
- ```--network kafka-net``` : this for settings the container to use spesific Docker Network in this case use ```kafka-net``` network
- ```-e ALLOW_ANONYMOUS_LOGIN=yes``` : this for set environtment variable so we can login to connect to zookeeper without credentials.
- ```bitnami/zookeeper:latest``` : this is spesiafic image that we wan to run in container.

### Testing Zookeeper Connection

![Testing Zookeeper Connection](https://github.com/wildangbudhi/Stream-Processing-using-Apache-Kafka/blob/master/1.%20Installation%20and%20Testing/Screenshoot/3.%20Testing%20Zookeeper%20Connections.png)

We can use ```telnet``` command to Zookeeper which in ```locahost``` and port ```2181``` because when we run the container we do Port Binding.

### Create and Run Apache Kafka Container

![Create and Run Apache Kafka Container](https://github.com/wildangbudhi/Stream-Processing-using-Apache-Kafka/blob/master/1.%20Installation%20and%20Testing/Screenshoot/4.%20Pull%20Image%2C%20Create%20Container%2C%20and%20Run%20Kafka.png)

We can use command bellow :

```bash
$ docker run --name kafka-server -d --network kafka-net -e ALLOW_PLAINTEXT_LISTENER=yes -e KAFKA_CFG_ZOOKEEPER_CONNECT=zookeeper-server:2181 -e KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092 -p 9092:9092 bitnami/kafka:latest
```

When we want to create and run Docker Container we need to have an image for the container. But ```docker run``` command automaticlly check wheter the image needed is available in local repository or not. If its not available in local repository the automaticlly pull required image from Docker Hub.

Parameters:
- ```--name kafka-server``` : is the name of container
- ```-d``` : For run the container on Deamon mode or background process
- ```--network kafka-net``` : this for settings the container to use spesific Docker Network in this case use ```kafka-net``` network
- ```-e KAFKA_CFG_ZOOKEEPER_CONNECT=zookeeper-server:2181``` : this for set environtment variable for connection to Zookeeper container. We can see address set is using Container Name, it possible because we have assign both Kafka Container and Zookeeper Container in same Network on Bridge Mode
- ```-e ALLOW_PLAINTEXT_LISTENER=yes``` : this for set environtment variable so kafka allowing plain text communication ( e.g SSL )
- ```-e KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092```: this for set environtment variable so that ZooKeeper can publish a public IP address and port for this broker to clients.

## Testing

For testing we can use Software called Conduktor.

![Testing Conduktor](https://github.com/wildangbudhi/Stream-Processing-using-Apache-Kafka/blob/master/1.%20Installation%20and%20Testing/Screenshoot/5.%20Testing%20Conduktor.png)