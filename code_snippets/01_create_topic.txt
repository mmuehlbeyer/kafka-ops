# 01_create_topic.sh
# create and modify kafka topic
# not intended to run without manual intervention ;-)

# create topic
kafka-topics --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic 5_minute_topic

# produce some data
kafka-console-producer --broker-list localhost:9092 --topic 5_minute_topic

# consume the data
kafka-console-consumer --bootstrap-server localhost:9092 --topic 5_minute_topic --from-beginning

#change retention to 5 minutes
kafka-configs --bootstrap-server localhost:9092 --alter --entity-type topics --entity-name 5_min_topic --add-config retention.ms=300000

# now observe the logs and check when the topic gets finally deleted
# output may be something like
## Deleting segment LogSegment(baseOffset=0, size=3412,
## lastModifiedTime=1615894643000, largestRecordTimestamp=Some(1615894616955)) due to retention time 3600000ms breach based on the ## largest record timestamp in t
## he segment (kafka.log.Log)

# now check the topics
# topic should be not listed any more
kafka-topics --list --bootstrap-server localhost:9092
