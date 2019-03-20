# Sprint 1

## Environment

### Vritual Box

Download Link: Https://www.virtualbox.org

### Hortonworks Sandbox (Hortonworks Data Platform 2.5)

Download Link: https://hortonworks.com/downloads

This is a image built for hadoop. Even though there are newer versions of the Hortonworks data platfrom Sandbox available, they require really beefy PCs to run, so in the interest of making sure we increase the odds of this actually running on your System, what we gonna do is to install an older version 2.5, and we just gonna update the individual components that needed. So the latest technology is still used.

### Setting up KafKa
Download Link: https://kafka.apache.org/quickstart

### Kafka Demo
Link: https://kafka.apache.org/21/documentation/streams/quickstart

### Dataset

Download Link: http://files.grouplens.org/datasets/movielens/ml-100k.zip

## Introduction to Hadoop

### Login in to Ambari

After using the ova file creat the virtual machine, you can use your local browser to enter 127.0.0.1:8888, and both the Username and Password are maria_dev

![ambari_signin](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/sprint1/signin_ambari.png)

![ambari_signin](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/sprint1/ambari.png)

### Built in upload function

We can try to use the Hive View function and choose the upload file, in the settings we choose field delimiter as 9 TAB(horizontal tab), and then we choose the local file u.data, set the table name as "ratings", each column as "user_id", "movie_id", "rating" and "rating_time"

![ambari_upload_gui](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/sprint1/upload_gui.png)

To set the delimiter to "124 |", and then upload u.item, call the table movie_names, set colums to movie_id and name

![ambari_upload_gui1](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/sprint1/upload_gui1.png)

### Built in Query

Choose the query tab, you can find the dataset uploaded in the leftside, and in the query editor you can add sql language, eg.

![ambari_query_ui](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/sprint1/query_gui.png)

### Visualization

Choose the visualization tab on the right hand, it is very easy to visualize. Choose movie_id as x and ratingcount as y, you can get the result

![ambari_query_ui](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/sprint1/visual.png)

## Without GUI

Use ssh maria@127.0.0.1 -p 2222 to access the virtual machine, and you can use the command line to control the hadoop

![no_ui](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/sprint1/without_gui.png)

To use the Python to create map reduce job, you have to first install pip. Enter 

```bash
su root
```

the default password is : hadoop, you can change it later.

In HDP 2.6 you can simply type

```bash
yum install python-pip
```

but in HDP 2.5, you have to first type

```bash
cd /etc/yum.repos.d
cp sandbox.repo /tmp
rm sandbox.repo
cd ~
yum install python-pip
```

```bash
pip install google-api-python-client==1.6.4
pip install mrjob==0.5.11
yum install nano
```

### Simple MapReduce example

Get the dataset from:

```
wget http://media.sundog-soft.com/hadoop/ml-100k/u.data
```

Get the mapreduce script from:

```
wget http://media.sundog-soft.com/hadoop/RatingsBreakdown.py
```

Run the hadoop MapReduce job:

```bash
python RatingsBreakdown.py -r hadoop --hadoop-streaming-jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar u.data
```

![mapreduce](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/sprint1/MapReduce.png)

You can use an advanced version so it shows the count of the movie as the first column. You can download the script here:

```bash
wget http://media.sundog-soft.com/hadoop/TopMovies.py
```

and then try the script locally:

```bash
python TopMovies.py u.data
```

