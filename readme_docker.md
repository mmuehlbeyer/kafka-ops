# docker-based-setup
the following will run a basic environment based on confluent platform 6 with the following components:

* 1 Zookeeper
* 1 Kafka Broker

get docker-compose.yml from [`docker-compose.yml`](docker-compose.yml).

start docker compose
```
docker-compose up -d
```

output should be similiar to

```
docker-compose up -d
Creating zookeeper ... done
Creating broker    ... done
```

check the container status  
```
docker ps
CONTAINER ID        IMAGE                             COMMAND                  CREATED             STATUS              PORTS                                            NAMES
a22601e2440a        confluentinc/cp-server:6.1.0      "/etc/confluent/dock…"   11 seconds ago      Up 9 seconds        0.0.0.0:9092->9092/tcp, 0.0.0.0:9101->9101/tcp   broker
cdcd4bfb6823        confluentinc/cp-zookeeper:6.1.0   "/etc/confluent/dock…"   12 seconds ago      Up 11 seconds       2888/tcp, 0.0.0.0:2181->2181/tcp, 3888/tcp       zookeeper
```

## docker based setup extended

docker setup with more services

* 1 Zookeeper
* 1 Kafka Broker
* Schema Registry
* Kafka Connect
* Confluent Control Center


get docker-compose.yml from [`docker-compose-extended.yml`](docker-compose-extended.yml).

start docker compose
```
docker-compose -f docker-compose-extended.yml up -d
```

output like 
```
docker-compose -f docker-compose-extended.yml up -d
Creating zookeeper ... done
Creating broker    ... done
Creating schema-registry ... done
Creating connect         ... done
Creating control-center  ... done
```

```
docker ps 
```
or
```
docker ps --format 'table {{.Names}}\t{{.Image}}\t{{.Status}}'

NAMES               IMAGE                                             STATUS
control-center      confluentinc/cp-enterprise-control-center:6.1.0   Up 8 minutes
connect             cnfldemos/cp-server-connect-datagen:0.4.0-6.1.0   Up 8 minutes
schema-registry     confluentinc/cp-schema-registry:6.1.0             Up 8 minutes
broker              confluentinc/cp-server:6.1.0                      Up 8 minutes
zookeeper           confluentinc/cp-zookeeper:6.1.0                   Up 8 minutes
```