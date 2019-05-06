# BigData Project

## Structure

Kafka -> Flume -> HDFS -> Pig -> HBase

## Kafka

### Intro Kafka

![Kafka_archi](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/final/kafka_archi.png)

- Kafka is a general-purpose publish / subscribe messaging system
- Kafka servers store all incoming messages from publishers for some period of time, and publishes them to a stream of data called a topic
- Kafka consumers subscribe to one or more topics, and receive data as it's published
- A stream/topic can have many different consumers, all with their own position in the stream maintained
- It's not just for Hadoop

### How Kafka Scales

![Kafka_scale](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/final/kafka_scale.png)

- Kafka itself may be distributed among many processes on many servers
- Consumers may also be distributed

