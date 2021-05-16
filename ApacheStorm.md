# Apache Storm

## Why Apache Storm?
* Exponential growth of data raised the need for advanced data capturing and processing capabilties.
* Besides Apache Hadoop which specializes with Batch processing, for real-time stream processing Storm is the best fit.
* Many use-case which requires real-time processing:
    * monitoring social media on sentiments of the election-related posts

#### Storm is to real-time processing what Hadoop is to batch processing.


### Use cases
 * Stream processing
 * Continuous computation
 * Real-time analytics

### Features
  * Streaming - Key enabler for Lambda Architecture
  * Fast - 1M+ messages per second per node (COund be even bigger)
  * Horizontally Scalable - Thousands of workers per cluster.
  * Fault Tolerant - Failure is expected and embraced
  * Reliable - Guaranteed message delivery, Exactly-once semantics.

 ### Storm Components
 * A storm cluster follows a master-slave model. 
 * Master and slave processes are coordinated through Zookeeper.
   

# Storm Data model

## Tuple
* Tuple is a basic unit of data that can be processed.
* Immutable ordered set of serializable value.
* Fields must have assigned name.
* Allows defining custom data types which can be serialized as fields in a tuple.
* Tuple is dynamically typed.

### Stream
* Core abstractio. of Storm.
* Unbounded sequence of Tuples. (No rigid boundry for the flowing data)

### Spout
* Componenet - either we build or reuse pre built.
* Source of streams.
* Wrap an event source and emit Tuples.
* Responsible for reading or listening to data from an external source
* A spout can emit multiple streams each of diff schema.

For instance, messge queue are often the source of the data processed by Strom. Storm Spout integerate with many types of message queues.

OS/Service/AppSensors -> Logs/events/errors/status-msgs -> Kafka/JMS/RabbitMQ -> Storm


### Bolt
* Core unit of compuatation - Processing module.
* Responsible for transforming a stream.
* Can finally writes to a data store.
* Can even from a data store.
* Optionally emit additional Streams.
* Ideally each bolt in the topology should be doing a simple transformation of tuple.

## Topology
* Abstraction that defines the graph of the computation.
* DAG of Stpouts and Bolts.
* Create a Storm topology and deploy on a storm cluster.
* Storm executes Spouts and Bolts as Tasks that run in parallel on multiple machines.

### Parallel execution of Topology components
There can be multiple bolts listening for the streams from the same Spout. There 2 bolts run tasks in parallel.

### Stream Grouping
* Shuffle - Randomized round-robin - Evenly distribute load to doownstream Bolts.
* Fields - Ensure all Tples with same field value are always routed to the same Task. (Ex: avarage by zip code, all tuples with same zip field endup in the same bolt).
* ALL - Replicate Streams across all the Bolt's Tasks (use with care)

Ex: Simple word count topology: This uses both Shuffle and Fields grouping: 

LineReaderSpout --(shuffle grp)--> WordSplitterBolt --(Fields grp)--> WordCountBolt

### Java APIs

```java
Spout {

  open() {
    // Initialize resources 
  }
  nextTuble() {
    // perform event sourcing
    this.collector.emit(tuple)
  }
}
```

```java
Bolt {
  execute(Tuple, OutputCollector) {
      String value = tuple.getField("FieldName");
      // processing and generate output
      outputCollector.emit(output)
  }
}
```


 # Storm Components
 ## Nimbus
   * Master node.
   * Distributes application code among worker nodes.
   * Assigning and monitoring tasks.
   * Stateless (No state info is stored in Nimbus). Relies on ZK for configs and state info.
   * Single Nimbus node per storm cluster.
   * Designed to fail-fast. When Nimbus dies, it can be restarted without having any effects on the already running tasks on workers.
   
 ## Supervisor nodes
   * Worker nodes performing the tasks assigned by Nimbus.
   * Each supervisor runs supervisor daemon.
   * Daemon is responsible for creating starting and stopping worker processes to execute tasks assigned to that node.

 ## Zookeeper
   * Distributed configuration, for coordinating b/w Nimbus and Supervisors.
   * Nimbus and Supervisor do not communicate directly but through ZooKeeper.

## Topology Deployment process
* Connect to Nimbus and submit required jars for deploying a toplogy.
* Nimbus taslks to Zookeeper to update configs.
* Supervisor listens to Zookeeper for config changes. 
* Supervisors bring back jars from Nimbus.
* As needed Supervisors launches the Tasks to run tasks.

# Reliable Processing
If something fails while processing, bolt can ACK or Fail a particular tuple.
* Bolt can Acknowlege that a Tuple has been processed successfully.
* Bolt can also Fail a tuple to trigger a spout to replay the original.


# Usecase - Kafka > Storm > HBase

Kafka -> KafkaSpout -> ParseBolt -> FilterBolt -> KafkaSinkBolt -> Kafka

FilterBolt ---> HBaseSinkBolt

# Usecase - Twitter

## What's hot on Twitter
* Trending tweets change may be every few minutes.
* Batch processing all tweets in last 10 mins can cause additional delays.
* Instead Storm can process each tweet as it comes in Near-Real-Time.
* Stream processing gets the most recent view of events flowing.

Distribute the stream to multiple components.

## Trending tweets topology
Spout -> SplitBolt -> CountBolt -> SortBolt

Multiple instances of SplitBolt and CountBolt
One instace of SortBolt.










