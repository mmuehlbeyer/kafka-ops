# Kafka examples

Some examples working with Kafka

## topic related stuff

### create a topic
```bash
kafka-topics --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic Test123

[...]

Created topic Test123.
```

### list topics
```bash
kafka-topics --list --bootstrap-server localhost:9092
```

should result in 
```bash
kafka-topics --list --bootstrap-server localhost:9092
Test123
__consumer_offsets
_confluent-license
_confluent-telemetry-metrics
_confluent_balancer_api_state
_confluent_balancer_broker_samples
```

### produce some data 
```bash
kafka-bash-producer --broker-list localhost:9092 --topic Test123
```

### consume the data
```bash
kafka-bash-consumer --bootstrap-server localhost:9092 --topic Test123 --from-beginning
```

### alter topics
#### increase the number of partitions
```bash
kafka-topics --bootstrap-server localhost:9092 --topic Test123 --alter --partitions 5
```
#### change retention (default 7 days)
kafka-configs --bootstrap-server localhost:9092 --alter --entity-type topics --entity-name 5_min_topic --add-config retention.ms=300000

### describe topics

#### specific topic
```bash
kafka-topics --bootstrap-server localhost:9092 --topic Test123 --describe
```

#### all topics
```bash
kafka-topics --bootstrap-server localhost:9092 --describe
```

### topics with under replicated partitions
```bash
kafka-topics --bootstrap-server localhost:9092 --describe --under-replicated-partition
```

