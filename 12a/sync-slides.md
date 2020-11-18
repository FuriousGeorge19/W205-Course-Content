---
title: Fundamentals of Data Engineering
author: Week 12 - sync session
...

---


#
## Project Review
- Review your Project 3
- Get ready to share
- `docker pull midsw205/base:latest`
- `git pull` in `~/w205/course-content`

::: notes

- Breakout at about 5 after the hour:
- Check in with each group 
- have students share screen
:::


#


## { data-background="images/pipeline-steel-thread-for-mobile-app.svg" } 

::: notes
Let's walk through this
- user interacts with mobile app
- mobile app makes API calls to web services
- API server handles requests:
    - handles actual business requirements (e.g., process purchase)
    - logs events to kafka
- spark then:
    - pulls events from kafka
    - filters/flattens/transforms events
    - writes them to storage
- presto then queries those events
:::


# 
## Flask-Kafka-Spark-Hadoop-Presto Part I
::: notes
- last week we did spark from files
- ended with spark files reading from kafka, did some munging events, extracted events, json explode, did some filtering for event types.
:::

#
## Setup

## Set up directory, get docker-compose
```
mkdir ~/w205/full-stack/
cd ~/w205/full-stack
cp ~/w205/course-content/12a/docker-compose.yml .
cp ~/w205/course-content/12a/*.py .
cp ~/w205/course-content/12a/*.sh .
```

::: notes
:::


## The `docker-compose.yml` 

Create a `docker-compose.yml` with the following
```
---
version: '2'
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:latest
    environment:
      ZOOKEEPER_CLIENT_PORT: 32181
      ZOOKEEPER_TICK_TIME: 2000
    expose:
      - "2181"
      - "2888"
      - "32181"
      - "3888"
    extra_hosts:
      - "moby:127.0.0.1"

  kafka:
    image: confluentinc/cp-kafka:latest
    depends_on:
      - zookeeper
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:32181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:29092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
    expose:
      - "9092"
      - "29092"
    extra_hosts:
      - "moby:127.0.0.1"

  cloudera:
    image: midsw205/cdh-minimal:latest
    expose:
      - "8020" # nn
      - "50070" # nn http
      - "8888" # hue
    #ports:
    #- "8888:8888"
    extra_hosts:
      - "moby:127.0.0.1"

  spark:
    image: midsw205/spark-python:0.0.5
    stdin_open: true
    tty: true
    volumes:
      - ~/w205:/w205
    expose:
      - "8888"
    ports:
      - "8888:8888"
    depends_on:
      - cloudera
    environment:
      HADOOP_NAMENODE: cloudera
    extra_hosts:
      - "moby:127.0.0.1"
    command: bash

  mids:
    image: midsw205/base:0.1.9
    stdin_open: true
    tty: true
    volumes:
      - ~/w205:/w205
    expose:
      - "5000"
    ports:
      - "5000:5000"
    extra_hosts:
      - "moby:127.0.0.1"
```

and with no need for a datafile on this one.

::: notes
:::

## Spin up the cluster

```
docker-compose up -d
```

::: notes
Now spin up the cluster
```
docker-compose up -d
```
:::


## Create a topic `events`

```
docker-compose exec kafka kafka-topics --create --topic events --partitions 1 --replication-factor 1 --if-not-exists --zookeeper zookeeper:32181
```


::: notes
First, create a topic `events`
```
docker-compose exec kafka kafka-topics --create --topic events --partitions 1 --replication-factor 1 --if-not-exists --zookeeper zookeeper:32181
```
which should show

    Created topic "events".
::: 

# 
## Web-app

- Take our instrumented web-app from before
`~/w205/full-stack/game_api.py`

```python
#!/usr/bin/env python
import json
from kafka import KafkaProducer
from flask import Flask, request

app = Flask(__name__)
producer = KafkaProducer(bootstrap_servers='kafka:29092')


def log_to_kafka(topic, event):
    event.update(request.headers)
    producer.send(topic, json.dumps(event).encode())


@app.route("/")
def default_response():
    default_event = {'event_type': 'default'}
    log_to_kafka('events', default_event)
    return "This is the default response!\n"


@app.route("/purchase_a_sword")
def purchase_a_sword():
    purchase_sword_event = {'event_type': 'purchase_sword'}
    log_to_kafka('events', purchase_sword_event)
    return "Sword Purchased!\n"
```

::: notes
full blown one that adds in request headers
:::

## run flask
```
docker-compose exec mids \
  env FLASK_APP=/w205/full-stack/game_api.py \
  flask run --host 0.0.0.0
```

::: notes

```
docker-compose exec mids env FLASK_APP=/w205/full-stack/game_api.py flask run --host 0.0.0.0
```

:::

## Set up to watch kafka

```
docker-compose exec mids \
  kafkacat -C -b kafka:29092 -t events -o beginning
```


::: notes
- new terminal window, leave up
- running kafkacat without -e so it will run continuously

```
docker-compose exec mids kafkacat -C -b kafka:29092 -t events -o beginning
```
:::

## Apache Bench to generate data

```
docker-compose exec mids \
  ab \
    -n 10 \
    -H "Host: user1.comcast.com" \
    http://localhost:5000/
```
```
docker-compose exec mids \
  ab \
    -n 10 \
    -H "Host: user1.comcast.com" \
    http://localhost:5000/purchase_a_sword
```
```
docker-compose exec mids \
  ab \
    -n 10 \
    -H "Host: user2.att.com" \
    http://localhost:5000/
```
```
docker-compose exec mids \
  ab \
    -n 10 \
    -H "Host: user2.att.com" \
    http://localhost:5000/purchase_a_sword
```

::: notes
- Choose to generate events with apache bench, curl from browser, but not mixing for now.
- generating 10 events for now, can up that as much as needed, e.g., 100K

```
docker-compose exec mids ab -n 10 -H "Host: user1.comcast.com" http://localhost:5000/
```
```
docker-compose exec mids ab -n 10 -H "Host: user1.comcast.com" http://localhost:5000/purchase_a_sword
```
```
docker-compose exec mids ab -n 10 -H "Host: user2.att.com" http://localhost:5000/
```
```
docker-compose exec mids ab -n 10 -H "Host: user2.att.com" http://localhost:5000/purchase_a_sword
```

:::

# 
## More Spark

## last time
`~/w205/spark-from-files/separate_events.py`

```python
#!/usr/bin/env python
"""Extract events from kafka and write them to hdfs
"""
import json
from pyspark.sql import SparkSession, Row
from pyspark.sql.functions import udf


@udf('string')
def munge_event(event_as_json):
    event = json.loads(event_as_json)
    event['Host'] = "moe"
    event['Cache-Control'] = "no-cache"
    return json.dumps(event)


def main():
    """main
    """
    spark = SparkSession \
        .builder \
        .appName("ExtractEventsJob") \
        .getOrCreate()

    raw_events = spark \
        .read \
        .format("kafka") \
        .option("kafka.bootstrap.servers", "kafka:29092") \
        .option("subscribe", "events") \
        .option("startingOffsets", "earliest") \
        .option("endingOffsets", "latest") \
        .load()

    munged_events = raw_events \
        .select(raw_events.value.cast('string').alias('raw'),
                raw_events.timestamp.cast('string')) \
        .withColumn('munged', munge_event('raw'))

    extracted_events = munged_events \
        .rdd \
        .map(lambda r: Row(timestamp=r.timestamp, **json.loads(r.munged))) \
        .toDF()

    sword_purchases = extracted_events \
        .filter(extracted_events.event_type == 'purchase_sword')
    sword_purchases.show()
    # sword_purchases \
        # .write \
        # .mode("overwrite") \
        # .parquet("/tmp/sword_purchases")

    default_hits = extracted_events \
        .filter(extracted_events.event_type == 'default')
    default_hits.show()
    # default_hits \
        # .write \
        # .mode("overwrite") \
        # .parquet("/tmp/default_hits")


if __name__ == "__main__":
    main()
```


::: notes
- single data frame created
- filter on event type
:::

## which we ran

```
docker-compose exec spark \
  spark-submit /w205/spark-from-files/separate_events.py
```

::: notes
```
docker-compose exec spark spark-submit /w205/spark-from-files/separate_events.py
```
:::

## what if different event types have different schema?

##
`~/w205/full-stack/just_filtering.py`

```python
#!/usr/bin/env python
"""Extract events from kafka and write them to hdfs
"""
import json
from pyspark.sql import SparkSession, Row
from pyspark.sql.functions import udf


@udf('boolean')
def is_purchase(event_as_json):
    event = json.loads(event_as_json)
    if event['event_type'] == 'purchase_sword':
        return True
    return False


def main():
    """main
    """
    spark = SparkSession \
        .builder \
        .appName("ExtractEventsJob") \
        .getOrCreate()

    raw_events = spark \
        .read \
        .format("kafka") \
        .option("kafka.bootstrap.servers", "kafka:29092") \
        .option("subscribe", "events") \
        .option("startingOffsets", "earliest") \
        .option("endingOffsets", "latest") \
        .load()

    purchase_events = raw_events \
        .select(raw_events.value.cast('string').alias('raw'),
                raw_events.timestamp.cast('string')) \
        .filter(is_purchase('raw'))

    extracted_purchase_events = purchase_events \
        .rdd \
        .map(lambda r: Row(timestamp=r.timestamp, **json.loads(r.raw))) \
        .toDF()
    extracted_purchase_events.printSchema()
    extracted_purchase_events.show()


if __name__ == "__main__":
    main()
```

::: notes
- usually going to have default responses with our events
- so will have different schemas,
- going to start doing filtering on the raw json before we explode it up into schema aware df
- send it through boolean udf here to filter on event type
- also write to hdfs as before
- this is just one approach, what's another?
:::

## run this

```
docker-compose exec spark \
  spark-submit /w205/full-stack/just_filtering.py
```

::: notes
```
docker-compose exec spark spark-submit /w205/full-stack/just_filtering.py
```

# 
## Write Events

::: notes
:::

##
`full-stack/filtered_writes.py`

```python
#!/usr/bin/env python
"""Extract events from kafka and write them to hdfs
"""
import json
from pyspark.sql import SparkSession, Row
from pyspark.sql.functions import udf


@udf('boolean')
def is_purchase(event_as_json):
    event = json.loads(event_as_json)
    if event['event_type'] == 'purchase_sword':
        return True
    return False


def main():
    """main
    """
    spark = SparkSession \
        .builder \
        .appName("ExtractEventsJob") \
        .getOrCreate()

    raw_events = spark \
        .read \
        .format("kafka") \
        .option("kafka.bootstrap.servers", "kafka:29092") \
        .option("subscribe", "events") \
        .option("startingOffsets", "earliest") \
        .option("endingOffsets", "latest") \
        .load()

    purchase_events = raw_events \
        .select(raw_events.value.cast('string').alias('raw'),
                raw_events.timestamp.cast('string')) \
        .filter(is_purchase('raw'))

    extracted_purchase_events = purchase_events \
        .rdd \
        .map(lambda r: Row(timestamp=r.timestamp, **json.loads(r.raw))) \
        .toDF()
    extracted_purchase_events.printSchema()
    extracted_purchase_events.show()

    extracted_purchase_events \
        .write \
        .mode('overwrite') \
        .parquet('/tmp/purchases')


if __name__ == "__main__":
    main()
```

::: notes
:::

## run this

```
docker-compose exec spark \
  spark-submit /w205/full-stack/filtered_writes.py
```

::: notes
```
docker-compose exec spark spark-submit /w205/full-stack/filtered_writes.py
```

:::


## should see purchases in hdfs

```
docker-compose exec cloudera hadoop fs -ls /tmp/purchases/
```

::: notes
:::

# 
## Queries From Spark

::: notes
:::

## spin up a notebook

```
docker-compose exec spark \
  env \
    PYSPARK_DRIVER_PYTHON=jupyter \
    PYSPARK_DRIVER_PYTHON_OPTS='notebook --no-browser --port 8888 --ip 0.0.0.0 --allow-root' \
  pyspark
```

::: notes
```
docker-compose exec spark env PYSPARK_DRIVER_PYTHON=jupyter PYSPARK_DRIVER_PYTHON_OPTS='notebook --no-browser --port 8888 --ip 0.0.0.0 --allow-root' pyspark
```

- use a notebook as our pyspark driver
:::


## New python3 notebook and play

```
purchases = spark.read.parquet('/tmp/purchases')
purchases.show()
purchases.registerTempTable('purchases')
purchases_by_example2 = spark.sql("select * from purchases where host='user1.comcast.com'")
purchases_by_example2.show()
newdf = purchases_by_example2.toPandas()
newdf.describe()
```

::: notes
:::

#
## Queries from Presto

## Hive metastore

- Track schema
- Create a table

::: notes
- The Hive metastore is a really common tool used to keep track of schema for
tables used throughout the Hadoop and Spark ecosystem (schema registry).

- To "expose" the schema for our "purchases"... we need to create a table in the
hive metastore.

- In hadoop ecosystem, 
- hive is a full on query engine, we don't use it any longer b/c it's slow, but we use the schema registry
- The hive metastore is friendly with multiple partitions being stored on the fs, everything that talks to hadoop can talk to the hive metastore. 
- We write it with spark and we want to read it with presto, to get them to agree we track the schema with hive metastore
- Hive server you usually interface with the thrift server (a seriazation critter) but it's actually set up as a relational db, mysql or postgresql, tracking these table names have these fields etc
- We have a hive metastore spun up in our cloudera container(that's why we needed a new cloudera container)

- There are two ways
  * Run hive explicitly and create an external table
  * Run spark, create a 

:::

## Hard Way


```
docker-compose exec cloudera hive
```


::: notes
- Run hive in the hadoop container using the hive command line
- This is what you would do, don't need to actually do it, skip to easier way
- This is deprecated at this point
:::

## 

```sql
create external table if not exists default.purchases2 (
    Accept string,
    Host string,
    User_Agent string,
    event_type string,
    timestamp string
  )
  stored as parquet 
  location '/tmp/purchases'
  tblproperties ("parquet.compress"="SNAPPY");
```

::: notes
```
create external table if not exists default.purchases2 (Accept string, Host string, User_Agent string, event_type string, timestamp string) stored as parquet location '/tmp/purchases'  tblproperties ("parquet.compress"="SNAPPY");
```


:::

## Or... we can do this an easier way


```
docker-compose exec spark pyspark
```


::: notes
- run spark
- what we want to do is run another spark job to start up pyspark, could do spark nb etc
:::


##

```python
df = spark.read.parquet('/tmp/purchases')
df.registerTempTable('purchases')
query = """
create external table purchase_events
  stored as parquet
  location '/tmp/purchase_events'
  as
  select * from purchases
"""
spark.sql(query)
```

::: notes
```
spark.sql("create external table purchase_events stored as parquet location '/tmp/purchase_events' as select * from purchases")
```
- read parquet from what we wrote into hdfs
- register temp table
- create external table purchase event
- store as parquet
- similar to what we saw in hard example
- we're still going to cheat and implicitly infer schema - but just getting it by select * from another df
:::

## Can just include all that in job

```python
#!/usr/bin/env python
"""Extract events from kafka and write them to hdfs
"""
import json
from pyspark.sql import SparkSession, Row
from pyspark.sql.functions import udf


@udf('boolean')
def is_purchase(event_as_json):
    event = json.loads(event_as_json)
    if event['event_type'] == 'purchase_sword':
        return True
    return False


def main():
    """main
    """
    spark = SparkSession \
        .builder \
        .appName("ExtractEventsJob") \
        .enableHiveSupport() \
        .getOrCreate()

    raw_events = spark \
        .read \
        .format("kafka") \
        .option("kafka.bootstrap.servers", "kafka:29092") \
        .option("subscribe", "events") \
        .option("startingOffsets", "earliest") \
        .option("endingOffsets", "latest") \
        .load()

    purchase_events = raw_events \
        .select(raw_events.value.cast('string').alias('raw'),
                raw_events.timestamp.cast('string')) \
        .filter(is_purchase('raw'))

    extracted_purchase_events = purchase_events \
        .rdd \
        .map(lambda r: Row(timestamp=r.timestamp, **json.loads(r.raw))) \
        .toDF()
    extracted_purchase_events.printSchema()
    extracted_purchase_events.show()

    extracted_purchase_events.registerTempTable("extracted_purchase_events")

    spark.sql("""
        create external table purchases
        stored as parquet
        location '/tmp/purchases'
        as
        select * from extracted_purchase_events
    """)


if __name__ == "__main__":
    main()
```

::: notes
- Modified filtered_writes.py to register a temp table and then run it from w/in spark itself

:::

## Run this

```
docker-compose exec spark spark-submit /w205/full-stack/write_hive_table.py
```

## See it wrote to hdfs

```
docker-compose exec cloudera hadoop fs -ls /tmp/
```
::: notes
- This is the first spark job to run - it does it all, read, flatten, write, ?query
:::
## and now ...

- Query this with presto

```
docker-compose exec presto presto --server presto:8080 --catalog hive --schema default
```

::: notes
- Presto just a query engine
- it's talking to the hive thrift server to get the table we just added
- connected to hdfs to get the data
- Querying with presto instead of spark bc presto scales well, handles a wider range of sql syntax, can start treating like a database, can configure it to talk to cassandra, s3 directly, kafka directly, mysql, good front end for your company's data lake
- We're overloading the word presto here

:::

## What tables do we have in Presto?

```
presto:default> show tables;
   Table   
-----------
 purchases 
(1 row)

Query 20180404_224746_00009_zsma3, FINISHED, 1 node
Splits: 2 total, 1 done (50.00%)
0:00 [1 rows, 34B] [10 rows/s, 342B/s]
```

## Describe `purchases` table

```
presto:default> describe purchases;
   Column   |  Type   | Comment 
------------+---------+---------
 accept     | varchar |         
 host       | varchar |         
 user-agent | varchar |         
 event_type | varchar |         
 timestamp  | varchar |         
(5 rows)

Query 20180404_224828_00010_zsma3, FINISHED, 1 node
Splits: 2 total, 1 done (50.00%)
0:00 [5 rows, 344B] [34 rows/s, 2.31KB/s]
```

## Query `purchases` table

```
presto:default> select * from purchases;
 accept |       host        |   user-agent    |   event_type   |        timestamp        
--------+-------------------+-----------------+----------------+-------------------------
 */*    | user1.comcast.com | ApacheBench/2.3 | purchase_sword | 2018-04-04 22:36:13.124 
 */*    | user1.comcast.com | ApacheBench/2.3 | purchase_sword | 2018-04-04 22:36:13.128 
 */*    | user1.comcast.com | ApacheBench/2.3 | purchase_sword | 2018-04-04 22:36:13.131 
 */*    | user1.comcast.com | ApacheBench/2.3 | purchase_sword | 2018-04-04 22:36:13.135 
 */*    | user1.comcast.com | ApacheBench/2.3 | purchase_sword | 2018-04-04 22:36:13.138
 ...
 ```


# 
## down

    docker-compose down

::: notes
:::



#
## Summary


#

<img class="logo" src="images/berkeley-school-of-information-logo.png"/>

