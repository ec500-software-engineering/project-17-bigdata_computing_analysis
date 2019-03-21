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

### Ambari

#### Login in to Ambari

After using the ova file creat the virtual machine, you can use your local browser to enter 127.0.0.1:8888, and both the Username and Password are maria_dev

![ambari_signin](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/sprint1/signin_ambari.png)

![ambari_signin](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/sprint1/ambari.png)

#### Admin password reset

If you want to use the admin account, you have to reset the password first.

Due to possibility of passwords being vulnerable to being hacked, we recommend
you change your Ambari admin password to be unique.

1. Open [Shell Web Client](http://sandbox-hdp.hortonworks.com:4200/) (aka Shell-in-a-Box):
2. The login using credentials: **root** / **hadoop**
3. Type the following commands: `ambari-admin-password-reset`

> IMPORTANT: The first time you login as **root**, you may be required to change the password – remember it!

This is not the reset for the password of the virtual machine! This is only the Ambari password change.

#### Built in upload function

We can try to use the Hive View function and choose the upload file, in the settings we choose field delimiter as 9 TAB(horizontal tab), and then we choose the local file u.data, set the table name as "ratings", each column as "user_id", "movie_id", "rating" and "rating_time"

![ambari_upload_gui](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/sprint1/upload_gui.png)

To set the delimiter to "124 |", and then upload u.item, call the table movie_names, set colums to movie_id and name

![ambari_upload_gui1](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/sprint1/upload_gui1.png)

#### Built in Query

Choose the query tab, you can find the dataset uploaded in the leftside, and in the query editor you can add sql language, eg.

![ambari_query_ui](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/sprint1/query_gui.png)

#### Visualization

Choose the visualization tab on the right hand, it is very easy to visualize. Choose movie_id as x and ratingcount as y, you can get the result

![ambari_query_ui](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/sprint1/visual.png)

### MapReduce 

#### Prerequisites

Use ssh maria@127.0.0.1 -p 2222 to access the virtual machine (if you are using windows please download PUTTY or xshell etc.), and you can use the command line to control the hadoop

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

#### Simple MapReduce example

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

You can use an advanced version so it shows the movie sorted by the count. You can download the script here:

```bash
wget http://media.sundog-soft.com/hadoop/TopMovies.py
```

and then try the script locally:

```bash
python TopMovies.py u.data
```

or start a new MapReduce job in hadoop:

```bash
python TopMovies.py -r hadoop --hadoop-streaming-jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar u.data
```

![mapreduce2](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/sprint1/MapReduce2.png)

### Pig

#### Why Pig?

- Writing mappers and reducers by hand takes a long time
- Pig introduces Pig Latin, a scripting language that lets you use SQL-like syntax to define your map and reduce steps
- Highly extensible with user-defined functions (UDF's)

![pig_structure](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/sprint1/pig_structure.png)

#### Pig Example

Firstly use the files view tab to upload u.data and u.item into the ml-100k folder.

Then choose the Pig View, and use the script below to find the oldest five star movies.

```pig
ratings = LOAD '/user/maria_dev/ml-100k/u.data' AS (userID:int, movieID:int, rating:int, ratingTime: int);

metadata = LOAD '/user/maria_dev/ml-100k/u.item' USING PigStorage('|')
	AS (movieID:int, movieTitle:chararray, releaseDate:chararray, videoRelease:chararray, imdbLink:chararray);
    
nameLookup = FOREACH metadata GENERATE movieID, movieTitle,
	ToUnixTime(ToDate(releaseDate, 'dd-MMM-yyyy')) AS releaseTime;
    
ratingsByMovie = GROUP ratings BY movieID;

avgRatings = FOREACH ratingsByMovie GENERATE group AS movieID, AVG(ratings.rating) AS avgRating;

fiveStarMovies = FILTER avgRatings BY avgRating > 4.0;

fiveStarsWithData = JOIN fiveStarMovies BY movieID, nameLookup BY movieID;

oldestFiveStarMovies = ORDER fiveStarsWithData BY nameLookup::releaseTime;

DUMP oldestFiveStarMovies;
```

![pig_script](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/sprint1/pig_script.png)

Then execute:

![pig_res](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/sprint1/pig_res.png)

You can try to run Pig on Tez. Tez uses what's called a directed acyclic graph to actually analyze all the interrelationships between the different steps that you;re doing and try to figure out the most optimal path for excuting things.

Click on the Execute on Tez button and the result is almost ten times faster.

#### Eg2

```pig
ratings = LOAD '/user/maria_dev/ml-100k/u.data' AS (userID:int, movieID:int, rating:int, ratingTime: int);

metadata = LOAD '/user/maria_dev/ml-100k/u.item' USING PigStorage('|')
	AS (movieID:int, movieTitle:chararray, releaseDate:chararray, videoRelease:chararray, imdbLink:chararray);

nameLookup = FOREACH metadata GENERATE movieID, movieTitle;

groupedRatings = GROUP ratings BY movieID;

averageRatings = FOREACH groupedRatings GENERATE group AS movieID,
	AVG(ratings.rating) AS avgRating, COUNT(ratings.rating) AS numRatings;
    
badMovies = FILTER averageRatings BY avgRating < 2.0;

namedBadMovies = JOIN badMovies BY movieID, nameLookup BY movieID;

res = FOREACH namedBadMovies GENERATE nameLookup::movieTitle AS movieName,
	badMovies::avgRating AS avgRating, badMovies::numRatings AS numRatings;

resSorted = ORDER res BY numRatings DESC;

DUMP resSorted;
```

![pig_res2](https://github.com/ec500-software-engineering/project-bigdata_computing_analysis/blob/master/documentation/sprint1/pig_res2.png)

#### Pig Latin

##### Relation

```pig
LOAD STORE DUMP
FILTER DISTINCT FOREACH/GENERATE MAPREDUCE STREAM SAMPLE
JOIN COGROUP GROUP CROSS CUBE
ORDER RANK LIMIT
UNION SPLIT
```

##### Diagnostics

```pig
DESCRIBE
EXPLAIN
ILLUSTRATE
```

##### UDF's

```pig
REGISTER
DEFINE
IMPORT
```

##### Other functions and loaders

```pig
AVG CONCAT COUNT MAX MIN SIZE SUM
PigStorage
TextLoader
JsonLoader
AvroLoader
ParquetLoader
OrcStorage
HBaseStorage
```

