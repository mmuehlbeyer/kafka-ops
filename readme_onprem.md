# on prem setup

The following describes a single node Kafka setup.
With ZooKeeper and Kafka installed on the same machine.
For a production environment the recommended way is to separate Zookeeper and Kafka.

## install open-jdk version 8 or 11
```console
sudo apt-get install openjdk-8-jdk
sudo apt-get install openjdk-11-jdk
```


## setup user and directories

### create user
For easier administration we use one user and group for Zookeeper and Kafka

```console
sudo addgroup --gid 3000 kafka

sudo adduser --uid 3000 --gid 3000 kafka

```
optional add new user kafka to sudo
```console
sudo usermod -aG sudo kafka
```

### create dirs

switch to newly created user and create the needed directories

```console
#directory used for Kafka binaries
sudo mkdir /app/
sudo chown -R kafka:kafka /app
```

```console
#directory for Kafka data
sudo mkdir /data/kafka -p
sudo mkdir /data/zookeeper -p
sudo chown -R kafka:kafka /data
```

## setup the software

### download
choose which edition you like to install

```console
#Confluent Enterprise
export conf_entp=http://packages.confluent.io/archive/7.2/confluent-7.2.2.tar.gz

#Confluent Community
export conf_comm=http://packages.confluent.io/archive/7.2/confluent-community-7.2.2.tar.gz

cd /app

curl -O $conf_entp

```

### extract the software 
```console
tar xvfz confluent-7.2.2.tar.gz
ln -s confluent-7.2.2 confluent

kafka@ubuntu01:/app$ ll
total 12
drwxr-xr-x  3 kafka kafka 4096 Oct  7 08:14 ./
drwxr-xr-x 21 root  root  4096 Oct  7 07:17 ../
lrwxrwxrwx  1 kafka kafka   15 Oct  7 08:13 confluent -> confluent-7.2.2/
drwxr-xr-x  8 kafka kafka 4096 Sep 20 04:40 confluent-7.2.2/
```

### optional: add env variables to your profile or bash_profile

see [`kafka_env.profile`](./etc/kafka_env.profile)kafka_env.profile file for often used environment variables and aliases
to ease the kafka administration 



### configure and start zookeeper
```console
vi /app/confluent/etc/kafka/zookeeper.properties
```
adapt `datadir` parameter to

```
dataDir=/data/zookeeper
```

#### start zookeeper
```console
cd /app/confluent/
zookeeper-server-start etc/kafka/zookeeper.properties &
```

### configure and start kafka broker
```console
vi /app/confluent/etc/kafka/server.properties
```
adapt `log.dirs`to 

```
log.dirs=/data/kafka
```
#### start the broker
```console
cd /app/confluent/
kafka-server-start etc/kafka/server.properties &
```