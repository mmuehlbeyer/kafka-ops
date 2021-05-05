# Kafka Connect logfiles to Kafka topic

short how to push kafka connect logfiles to kafka topic as well as to local filesystem

logfiles will reside on default Kafka log location and in a separate Kafka topic

tested with Confluent OSS in version 6.1.0

---

### prerequisites

**Kafka binaries installed Kafka Cluster up and running**

**Kafka Topic for storing the logfils, e.g `k-connect-log`** 
  
  
**extra jar files**

add following files 

* common-logging-6.1.1.jar

* confluent-log4j-extensions-6.1.1.jar

to 

`$CONFLUENT_INSTALL_DIR/share/java/confluent-common`   (e.g. /app/confluent/share/java/confluent-common )

jars are available for download 

* https://mvnrepository.com/artifact/io.confluent/common-logging

* https://mvnrepository.com/artifact/io.confluent/confluent-log4j-extensions  
  
 <br/> 
**adapt kafka connect-log4j.properties**  

example config in connect-log4j.properties [`connect-log4j.properties`](connect-log4j.properties).

code snippet
```
log4j.appender.kafka_appender=org.apache.kafka.log4jappender.KafkaLog4jAppender
log4j.appender.kafka_appender.layout=io.confluent.common.logging.log4j.StructuredJsonLayout
log4j.appender.kafka_appender.BrokerList=localhost:9092
log4j.appender.kafka_appender.Topic=k-connect-log
log4j.logger.processing=INFO, kafka_appender

log4j.logger.org.apache.kafka.connect=INFO, kafka_appender
log4j.additivity.org.apache.kafka.connect=false
```
