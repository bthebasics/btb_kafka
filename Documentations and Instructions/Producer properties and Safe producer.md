## Producer properties

Some of the properties and descriptions : 

| properties             | description                                                  | value              |
| ---------------------- | ------------------------------------------------------------ | ------------------ |
| enable.idempotence     | to enable idempotence producer config ( read more )          | default false      |
| max.in.flight.requests | for Kafka < 1.1 = **1**  and for Kafka > 1.1  = **5**        | 1 or 5             |
| acks                   | decides type of acknowledgement                              | all or 1 or 0      |
| min.insync.replicas    | how many replications to have for msg before setting successful | 2                  |
| retries                | when fails to write how many times to retry                  | set to high number |

enable.idempotence  :   This enables idempotent producer , which is supposed to be stable and safe pipeline.



**Safe producer** 

for kafka < 0.11 

- acks=all ( producer level )
  - Ensures data is properly replicated before ack is received
- min.insync.replicas=2 ( broker/topic level)
  - Ensures two brokers at-least will have data
- retries-MAX_INT ( producer level )



when you set enable.idempotence = true ( producer level ) + min.insync.replicas=2  ( broker/topic level )

implies acks=all, retries=MAX_INT, max.in.flight.requests.per.connection=5 ( default )



```scala
 producerProperties.setProperty(ProducerConfig.ENABLE_IDEMPOTENCE_CONFIG, "true")
 producerProperties.setProperty(ProducerConfig.ACKS_CONFIG, "all")
 producerProperties.setProperty(ProducerConfig.RETRIES_CONFIG, Integer.toString(Integer.MAX_VALUE))
 producerProperties.setProperty(ProducerConfig.MAX_IN_FLIGHT_REQUESTS_PER_CONNECTION, "5")
```



### Message Compression

Compression helps: 

- much smaller producer request size
- Faster to transfer
- Better throughput
- Better disk utilization

**<u>Disadvantages</u>**

- additional CPU cycle for compression
- consumer size - CPU for decompression

use Snappy or LZ4 for optimal speed ( Test them All )

Always use compression in production and especially if you have high throughput

<u>***Consider tweaking -***</u>

- linger.ms 
- batch.size 

--------------------------

### Producer Batching

**linger.ms**  - number of seconds producer will wait before sending next batch out ( default 0 )

by increasing some lag ( linger.ms = 5 ), we increase the chances of messages being sent in batches , which increases throughput, compression and efficiency of producer

if batch is full before linger.ms , it will send right away

**batch.size** -  maximum number of bytes that will be included in batch ( default 16 kb)

- increase up to 32 or 64 kb - 
- any message bigger than batch size will not be batched
- batch is allocated per partition - so don't keep number too high

---

using snappy - lightweight and good throughput

```scala
producerProperties.setProperty(ProducerConfig.COMPRESSION_TYPE_CONFIG, "snappy")
producerProperties.setProperty(ProducerConfig.LINGER_MS_CONFIG, "20" ) // milliseconds
producerProperties.setProperty(ProducerConfig.BATCH_SIZE_CONFIG, Integer.toString(32*1024)) // 32 kb
```



### Kafka producer Partitioning

default partitioning in Kafka is "murmur2" algo

you can override the default partitioning behaviour ( partitioner.class ) 

**formula** 

targetPartition = Utils.abs( Utils.murmur2(recordKey()) % numPartitions )

- same key will go to same partition
- adding parttion will change alter formula and results



---------

### Max.block.ms and Buffer.memory

if producer produces faster than broker can take, records will be buffered

buffer.memory = 32MB   // size of send buffer

if buffer is full, so send method will block and wont return right away



max.block.ms = 60000 ( 60 seconds ) - the time send will block until throwing exception ( due to above )

- producer has filler the buffer
- broker not accepting
- 60 seconds elapsed

may caused by if broker is down - 















