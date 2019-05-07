[TOC]

<b>The first part is all about introduction of Hadoop or relative techniques. The second part is about our porject's detail information.</b>

# BigData Introduction

## Structure

Java input stream -> Text file -> Kafka -> Flume -> HDFS -> Pig -> HBase

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

./kafka-topics.sh --list --zookeeper sandbox.hortonworks.com:2181

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

## Flume

### What is Flume

- Another way to stream data into your cluster
- Made from the start with Hadoop in mind (Built in sink for HDFS and HBase)
- Originally made to handle log aggregation

Flume lets Hadoop users ingest high-volume streaming data into HDFS for storage. Specifically, Flume allows users to:

|         Feature         |                         Description                          |
| :---------------------: | :----------------------------------------------------------: |
|       Stream data       | Ingest streaming data from multiple sources into Hadoop for storage and analysis |
|    Insulate systems     | Buffer storage platform from transient spikes, when the rate of incoming data exceeds the rate at which data can be written to the destination |
| Guarantee data delivery | Flume NG uses channel-based transactions to guarantee reliable message delivery. When a message moves from one agent to another, two transactions are started, one on the agent that delivers the event and the other on the agent that receives the event. This ensures guaranteed delivery semantics |
|   Scale horizontally    |  To ingest new data streams and additional volume as needed  |

Enterprises use Flume’s powerful streaming capabilities to land data from high-throughput streams in the [Hadoop Distributed File System (HDFS)](https://hortonworks.com/hadoop/hdfs/). Typical sources of these streams are application logs, sensor and machine data, geo-location data and social media. These different types of data can be landed in Hadoop for future analysis using interactive queries in Apache Hive. Or they can feed business dashboards served ongoing data by Apache HBase.

In one specific example, Flume is used to log manufacturing operations. When one run of product comes off the line, it generates a log file about that run. Even if this occurs hundreds or thousands of times per day, the large volume log file data can stream through Flume into a tool for same-day analysis with Apache Storm or months or years of production runs can be stored in HDFS and analyzed by a quality assurance engineer using Apache Hive.

![flume](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/final/flume.png)

### Components of an agent

1. Source

- Where data is coming from
- Can optionally have Channel Selectors and Interceptors

2. Channel

- How the data is transferred(via memory or files)

3. Sink

- Where the data is going 
- Can be organized into Sink Groups
- A sink can connect to only one channel
- Channel is notified to delete a message once the sink processes it

## Spark

### Why Spark Streaming?

- Analyze data streams in real time, instead of in huge batch jobs daily
- Analyzing streams of web log data to react to user behavior
- Analyze streams of real-time sensor data for "Internet of Things" stuff
- Processing of RDD's can happen in parallel on different worker nodes

# Project - Market Trend 

## Kafka System 

As what we wrote in the user story, this system will be desgined for a market owner who want to check the trend in different products. 

Consider the first number as Merchandise_id(0-200), the second number as Customer_id(0-20000), write a Java file to write a text stream:

```java
import java.text.SimpleDateFormat;
import java.util.*;
import java.io.*;
import java.text.*;

public class Producer {

	public static void main(String[] args) throws IOException {
		String path = "samplelog.txt";
		File file = new File(path);
		if(!file.exists()){
			file.createNewFile();
		}
		class Output extends TimerTask  {
			public void run()  {
				DateFormat df = new SimpleDateFormat("dd/MM/yy HH:mm:ss");
				Date dateobj = new Date();
				int product = (int)(Math.random()*200);
				int user = (int)(Math.random()*20000);
				StringBuilder sb = new StringBuilder();
				sb.append(df.format(dateobj));
				sb.append(" ");
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

Copy three kafka settings file from /usr/hdp/current/kafka-broker/bin to ~/kafka_settings:

- connect-standalone.properties
- connect-file-sink.properties
- connect-file-source.properties

Change the filename in source to samplelog.txt, change topic to EC500, and change the host name to HDP's name

Use the command to start producer:

```shell
./connect-standalone.sh ~/kafka_settings/connect-standalone.properties ~/kafka_settings/connect-file-source.properties ~/kafka_settings/connect-file-sink.properties
```

Go back to kafka's folder, use the command to start consumer:

```shell
./kafka-console-consumer.sh --bootstrap-server sandbox.hortonworks:6667 --topic ec500 --zookeeper localhost:2181
```

And then you can see the information from the consumer side

## Flume System

After the information has been delivered by Kafka, the flume will handle the process later on. It will help us store the information into the HDFS

Set the conf as 

```shell
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
a1.sources.r1.type = exec
a1.sources.r1.command=tail -F /home/maria_dev/kafka_settings/samplelog.txt

# Describe the sink
a1.sinks.k1.type = hdfs
#a1.sinks.k1.hdfs.path = /user/maria_dev/flume/events/%y-%m-%d/%H%M/%S
a1.sinks.k1.hdfs.path = /user/maria_dev/flume/events/

# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```

Run the flume using the command:

```
bin/flume-ng agent --conf conf --conf-file ~/flume_settings/flconf.conf --name a1 -Dflume.root.logger=INFO,c
onsole
```

## Spark Streaming

### 