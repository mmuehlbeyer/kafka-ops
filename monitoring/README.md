ready to use demo monitoring setup with confluent platform and prometheus


install confluent platform

```wget https://packages.confluent.io/archive/7.2/confluent-community-7.2.0.tar.gz```

```
tar -xzf confluent-community-7.2.0.tar.gz -C /app
rm confluent-community-7.2.0.tar.gz
ln -s /app/confluent-7.2.0 /app/confluent
```

#### configure the jmx exporter part

```
mkdir /app/confluent/share/monitoring

wget -O /app/confluent/share/monitoring/zookeeper.yml https://raw.githubusercontent.com/confluentinc/jmx-monitoring-stacks/7.2-post/shared-assets/jmx-exporter/zookeeper.yml

wget -O /app/confluent/share/monitoring/kafka_broker.yml https://raw.githubusercontent.com/confluentinc/jmx-monitoring-stacks/7.2-post/shared-assets/jmx-exporter/kafka_broker.yml
```

#### start the kafka stack

with basic config (not production ready)

zookeeper
```
EXTRA_ARGS=-javaagent:/app/confluent/share/java/prometheus/jmx_prometheus_javaagent-0.16.1.jar=9101:/app/confluent/share/monitoring/zookeeper.yml /app/confluent/bin/zookeeper-server-start /app/confluent/etc/kafka/zookeeper.properties
```

kafka
```
EXTRA_ARGS=-javaagent:/app/confluent/share/java/prometheus/jmx_prometheus_javaagent-0.16.1.jar=9102:/app/confluent/share/monitoring/kafka_broker.yml /app/confluent/bin/kafka-server-start /app/confluent/etc/kafka/server.properties
```

#### prometheus

```
wget https://github.com/prometheus/prometheus/releases/download/v2.38.0/prometheus-2.38.0.linux-amd64.tar.gz

tar -xzf prometheus-2.38.0.linux-amd64.tar.gz -C /app

rm prometheus-2.38.0.linux-amd64.tar.gz

ln -s /app/prometheus-2.38.0.linux-amd64 /app/prometheu
```


get prometheus.yml

```
cd /app/prometheus

wget https://raw.githubusercontent.com/mmuehlbeyer/kafka-ops/master/monitoring/prometheus.yml
```


start prometheus

```
nohup ./prometheus --config.file=prometheus.yml > prom.log &
```