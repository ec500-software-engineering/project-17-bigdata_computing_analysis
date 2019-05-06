# BigData Introduction

## Structure

Java input stream to Text file -> Kafka -> Flume -> HDFS -> Pig -> HBase

## Kafka

### Intro Kafka

A streaming platform has three key capabilities:

- Publish and subscribe to streams of records, similar to a message queue or enterprise messaging system.
- Store streams of records in a fault-tolerant durable way.
- Process streams of records as they occur.

Kafka is generally used for two broad classes of applications:

- Building real-time streaming data pipelines that reliably get data between systems or applications
- Building real-time streaming applications that transform or react to the streams of data

To understand how Kafka does these things, let's dive in and explore Kafka's capabilities from the bottom up.

First a few concepts:

- Kafka is run as a cluster on one or more servers that can span multiple datacenters.
- The Kafka cluster stores streams of *records* in categories called *topics*.
- Each record consists of a key, a value, and a timestamp.

Kafka has four core APIs:

- The [Producer API](https://kafka.apache.org/documentation.html#producerapi) allows an application to publish a stream of records to one or more Kafka topics.
- The [Consumer API](https://kafka.apache.org/documentation.html#consumerapi) allows an application to subscribe to one or more topics and process the stream of records produced to them.
- The [Streams API](https://kafka.apache.org/documentation/streams) allows an application to act as a *stream processor*, consuming an input stream from one or more topics and producing an output stream to one or more output topics, effectively transforming the input streams to output streams.
- The [Connector API](https://kafka.apache.org/documentation.html#connect) allows building and running reusable producers or consumers that connect Kafka topics to existing applications or data systems. For example, a connector to a relational database might capture every change to a table.

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

### Simple Kafka Producer-Consumer Example

The path of Kafka in HDP2.5 is /usr/hdp/current/kafka-broker/,

```bash
cd /usr/hdp/current/kafka-broker/bin
./kafka-topics.sh --create --zookeeper sandbox.hortonworks.com:2181 --replication-factor 1 --partitions 1 --topic ec500
./kafka-console-producer.sh --broker-list sandbox.hortonworks.com:6667 --topic ec500
```

We use the port 6667 because we know it is available. Then we can use this producer to input messages, and open a second console as the customer

![Kafka_producer](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/final/kafka_producer.png)

Use the consumer script to start the consumer

```bash
cd /usr/hdp/current/kafka-broker/bin/
./kafka-console-consumer.sh --bootstrap-server sandbox.hotonworks.com:6667 --zookeeper localhost:2181 --topic ec500 --from-beginning
```

![Kafka_consumer](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/final/kafka_consumer.png)

Then the information is consumed. Then all the information will become real-time. What should be noticed is that the Kafka can store the information for some time.

# Project 

## Kafka System 

As what we wrote in the user story, this system will be desgined for a market owner who want to check the trend in different products. 

### Create Producer

Consider the first number as Merchandise_id(0-200), the second number as Customer_id(0-20000), write a Java file to write a text stream:

```java
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.Timer;
import java.util.TimerTask;
import java.io.*;

public class Producer {

	public static void main(String[] args) throws IOException {
		String path = "samplelog.txt";
		File file = new File(path);
		if(!file.exists()){
			file.createNewFile();
		}
		class Output extends TimerTask  {
			public void run()  {
				int product = (int)(Math.random()*200);
				int user = (int)(Math.random()*20000);
				StringBuilder sb = new StringBuilder();
				sb.append(product);
				sb.append(" ");
				sb.append(user);
				try{
					FileWriter fileWriter = new FileWriter("samplelog.txt",true);
					fileWriter.write(sb.toString()+"\n");
					fileWriter.close();
				}catch(Exception e){
					e.printStackTrace();
				}
			}
		}
		Timer timer = new Timer();
		timer.schedule(new Output(), 0, 1000);
	}
}
```

Put the Java file into the HDP, compile and run the java file. The log file will be generated named "samplelog.txt"