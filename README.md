# kafka_learning

https://kafka.apache.org/quickstart
![](./imgs/0.png)
![](./imgs/1.png)
![](./imgs/2.png)
![](./imgs/3.png)
![](./imgs/4.png)
![](./imgs/5.png)
![](./imgs/6.png)
![](./imgs/7.png)
![](./imgs/8.png)
![](./imgs/9.png)
![](./imgs/10.png)
![](./imgs/11.png)
![](./imgs/12.png)
![](./imgs/13.png)
![](./imgs/14.png)
![](./imgs/15.png)
![](./imgs/16.png)
![](./imgs/17.png)
![](./imgs/18.png)
![](./imgs/19.png)
![](./imgs/20.png)
![](./imgs/21.png)
![](./imgs/22.png)
![](./imgs/23.png)
![](./imgs/24.png)
![](./imgs/25.png)
![](./imgs/26.png)
![](./imgs/27.png)
![](./imgs/28.png)
![](./imgs/29.png)
![](./imgs/30.png)
![](./imgs/31.png)
![](./imgs/32.png)
![](./imgs/33.png)
![](./imgs/34.png)
![](./imgs/35.png)
![](./imgs/36.png)
![](./imgs/37.png)
![](./imgs/38.png)
![](./imgs/39.png)
![](./imgs/40.png)
![](./imgs/41.png)
![](./imgs/42.png)
![](./imgs/43.png)
![](./imgs/44.png)
![](./imgs/45.png)
![](./imgs/46.png)
![](./imgs/47.png)
![](./imgs/48.png)
![](./imgs/49.png)
![](./imgs/50.png)
![](./imgs/51.png)
![](./imgs/52.png)
![](./imgs/53.png)
![](./imgs/54.png)
![](./imgs/55.png)
![](./imgs/56.png)
![](./imgs/57.png)
![](./imgs/58.png)
![](./imgs/59.png)
![](./imgs/60.png)
![](./imgs/61.png)
![](./imgs/62.png)
![](./imgs/63.png)
![](./imgs/64.png)
![](./imgs/65.png)
![](./imgs/66.png)
![](./imgs/67.png)
![](./imgs/68.png)
![](./imgs/69.png)
![](./imgs/70.png)
![](./imgs/71.png)
![](./imgs/72.png)
![](./imgs/73.png)
![](./imgs/74.png)
![](./imgs/75.png)
![](./imgs/76.png)
![](./imgs/77.png)
![](./imgs/78.png)
![](./imgs/79.png)
![](./imgs/80.png)
![](./imgs/81.png)
![](./imgs/82.png)
![](./imgs/83.png)
![](./imgs/84.png)
![](./imgs/85.png)
![](./imgs/86.png)
![](./imgs/87.png)
![](./imgs/88.png)
![](./imgs/89.png)
![](./imgs/90.png)
![](./imgs/91.png)
![](./imgs/92.png)
![](./imgs/93.png)
![](./imgs/94.png)
![](./imgs/95.png)
![](./imgs/96.png)
![](./imgs/97.png)
![](./imgs/98.png)
![](./imgs/99.png)


## stream app

### Streams and state

1. Repartition

First, let’s have a general discussion on how repartitioning works (see figure 4.6). To repartition records, first you may modify or change the key on the original record, and then you write out the record to a new topic. Next, you consume those records again; but as a result of repartitioning, those records may come from different partitions than they were in originally.

Repartitioning in Kafka Streams is easily accomplished by using the KStream.through() method, as illustrated in figure 4.7. The KStream.through() method creates an intermediate topic, and the current KStream instance will start writing records to that topic. A new KStream instance is returned from the through() method call, using the same intermediate topic for its source. This way, the data is seamlessly repartitioned.

2. Failure recovery and fault tolerance

Application failure is inevitable, especially when it comes to distributed applications. We need to shift our focus from preventing failure to recovering quickly from failure, or even from restarts.


Backing up a state store with a topic may seem expensive, but there are a couple of mitigating factors at play: a KafkaProducer sends records in batches, and by default, records are cached. It’s only on cache flush that Kafka Streams writes records to the store, so only the latest record for a given key is persisted.

They’re local to the defined processors and don’t share access across processes or threads. State stores also use topics for backup and quick recovery.

There are two additional classes for customizing the state store: the Materialized and StoreBuilder classes. Which one you’ll use depends on how you add the store to the topology. If you use the high-level DSL, you’ll typically use the Materialized class; when you work with the lower-level Processor API, you’ll use the StoreBuilder.

It’s worth noting that all persistent StateStore instances provide local storage using RocksDB (http://rocksdb.org).

suppose you lost a machine running Kafka Streams. Once you recovered your server and restarted your Kafka Streams application, the state stores for that instance would be restored to their original contents (the last committed offset in the changelog before crashing).


3. Joining and repartiiton

whenever you invoke a method that could result in generating a new key (selectKey, map, or transform), an internal Boolean flag is set to true, indicating that the new KStream instance requires repartitioning. With this Boolean flag set, if you perform a join, reduce, or aggregation operation, the repartitioning is handled for you automatically.

You supply four parameters to the KStream.join method:

* electronicsStream—The stream of electronic purchases to join with.

* purchaseJoiner—An implementation of the ValueJoiner<V1, V2, R> interface. ValueJoiner accepts two values (not necessarily of the same type). The ValueJoiner.apply method performs the implementation-specific logic and returns a (possibly new) object of type R (maybe a whole new type). In this example, purchaseJoiner will add some relevant information from both Purchase objects, and it will return a CorrelatedPurchase object.

* twentyMinuteWindow—A JoinWindows instance. The JoinWindows.of method specifies a maximum time difference between the two values to be included in the join. In this case, the timestamps must be within 20 minutes of each other.

* A Joined instance—Provides optional parameters for performing joins. In this case, it’s the key and the value Serde for the calling stream, and the value Serde for the secondary stream. You only have one key Serde because, when joining records, keys must be of the same type.

In order to perform a join in Kafka Streams, you need to ensure that all join participants are co-partitioned, meaning that **they have the same number of partitions and are keyed by the same type**. As a result, when you call the join() method in listing 4.13, both KStream instances will be checked to see if a repartition is required.

4. TIMESTAMPS IN KAFKA STREAMS

* Event time—A timestamp set when the event occurred, usually embedded in the object used to represent the event. For our purposes, we’ll consider the timestamp set when the ProducerRecord is created as the event time as well.

* Ingestion time—A timestamp set when the data first enters the data processing pipeline. You can consider the timestamp set by the Kafka broker (assuming a configuration setting of LogAppendTime) to be ingestion time.

* Processing time—A timestamp set when the data or event record first starts to flow through a processing pipeline.

We’ll consider three cases of timestamp-processing semantics:

1. A timestamp embedded in the actual event or message object (event-time semantics)
2. Using the timestamp set in the record metadata when creating the ProducerRecord (event-time semantics)
3. Using the current timestamp (current local time) when the Kafka Streams application ingests the record (processing-time semantics)




### windows and time
https://blog.csdn.net/daydayup_668819/article/details/98593214

