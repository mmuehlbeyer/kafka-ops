# Kafka examples

Some examples working with Kafka

## topic related stuff

### create a topic
```console
kafka-topics --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic Test123

[...]

Created topic Test123.
```

### list topics
```console
kafka-topics --list --bootstrap-server localhost:9092
```

should result in 
```console
kafka-topics --list --bootstrap-server localhost:9092
Test123
__consumer_offsets
_confluent-license
_confluent-telemetry-metrics
_confluent_balancer_api_state
_confluent_balancer_broker_samples
```

### produce some data 
kafka-console-producer --broker-list localhost:9092 --topic Test123


### consume the data
kafka-console-consumer --bootstrap-server localhost:9092 --topic Test123 --from-beginning

