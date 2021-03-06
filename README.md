# Big data computing and analysis

[![Build Status](https://travis-ci.com/ec500-software-engineering/project-17-bigdata_computing_analysis.svg?branch=master)](https://travis-ci.com/ec500-software-engineering/project-17-bigdata_computing_analysis)

## Final:

Please Read the final.md for the final result.

https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/final.md

## Sprint 1:

https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/Sprint%201/readme.md

## User Story

- Mr.K is a large supermarket owner. He needs to manage thousands of different merchandise, and manually computing the sales and profit is hard to optimize the best solution of importing new goods. With the big data system, it is easy to find out what to import at certain time can gain the best value and bring the most profit for him.
- Mr.News owns a magazine company. He focus on the new trend of fashion and wants to grab the new trend so the content of the magazine will not be out of date. With the big data, he is able to find out where the trends are precisely and quickly.

## Purpose

1. Get stream data from the Internet
2. Use technologies like Kafka and Flume to handle stream data on linux
3. Store data to HBase or some high-level database
4. Write MapReduce code to map and reduce stream data, 
5. Store the results in the relational database like MySQL
6. Show the visualization results using technologies like SSM

![MapReduce](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/readme/map_reduce_eg.png)

## Architecture

![Structure](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/readme/structure.png)


## Sprint 1

- Study basic concepts and usages of big data tools like Kafka, HBase and Flume 
- Build the input data stream 
- Handle input stream with HDFS.

## HDFS

The Hadoop Distributed File System (HDFS) is a distributed file system designed to run on commodity hardware. It has many similarities with existing distributed file systems. However, the differences from other distributed file systems are significant. HDFS is highly fault-tolerant and is designed to be deployed on low-cost hardware. HDFS provides high throughput access to application data and is suitable for applications that have large data sets. HDFS relaxes a few POSIX requirements to enable streaming access to file system data. 

## Kafka

Kafka® is used for building real-time data pipelines and streaming apps. It is horizontally scalable, fault-tolerant, wicked fast, and runs in production in thousands of companies.

![Structure](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/readme/kafka-apis.png)

## Flume

Flume is a distributed, reliable, and available service for efficiently collecting, aggregating, and moving large amounts of log data. It has a simple and flexible architecture based on streaming data flows. It is robust and fault tolerant with tunable reliability mechanisms and many failover and recovery mechanisms. It uses a simple extensible data model that allows for online analytic application.

![Structure](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/readme/flume.png)
