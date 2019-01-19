# Consumer Properties

### delivery semantics

1) **At most once** : Message is committed as soon as batch is received

2) **At least once**  - Offsets are committed after processing of the message

3) **Exactly Once -** 



### consumer poll

Kafka has poll model, while most other sub-pub has push model, this helps consumer to control when they want to consume, how fast and gives ability to replay 

| Setting                      | description                                                  |
| ---------------------------- | ------------------------------------------------------------ |
| **fetch.min.bytes**          | how much data per request to be pulled, helps increasing throughput and decreasing request number, at cost of latency |
| **Max.poll.record**          | how many records per request to be received, increase if msg are small |
| **max.partition.fetch.size** | max data returned by broker per partition.( 1 MB )           |
| **fetch.max.bytes**          | default 50 MB - max data request for each fetch request      |

### consumer offset commits strategies

Strategy # 1: 

**enable.auto.commit** = true and synchronous processing of batches

if you don't specify synchronous processing, you will be in "at most once" behavior which is risky

```java
while(true) {
    List<records> batch = consumer.poll(Duration.ofMills(100))
      //  doSomethingSynchronous(batch)
}
```

**enable.auto.commit**  = false and manual commits of offsets

```java
while(true) {
    List<records> batch += consumer.poll(Duration.ofMills(100))
        if isReady(batch {
            doSomethingSynchronous(Batch)
                consumer.commitSync();
        }
      //  doSomethingSynchronous(batch)
}
```

### Consumer offset Reset Settings

| Setting                      | descriptions                               |
| ---------------------------- | ------------------------------------------ |
| auto.offset.reset = latest   | will read from end of the log              |
| auto.offset.reset = earliest | will read from start of the log            |
| auto.offset.reset = none     | will throw exception if no offset is found |

consumer offset can be lost - 

- if consumer hasn't read new data in 1 day ( Kafka < 2 )
- if consumer hasn't read new data in 7 day ( Kafka > 2 )



### Replaying data for consumer

To reply data for consumer group : 

- Take all the consumers from specific group down

- User Kafka-consumer-group command to set offset to what you want

  

  **Guideline**

- Set proper data retention period and offset retention period
- ensure the auto offset reset behavior is one you expect or you want



```shell
# reset the offset of the consumer group to the earliest 
kafka-consumer-groups --boostrap-server 127.0.0.1:9092 --group kafka-demo-elasticSearch --reset-offsets --execute --to-earliest --topic tweeter_tweets

## make sure consumer is idempotent
```

### Consumer group liveliness

<a href="https://ibb.co/XXLGfy0"><img src="https://i.ibb.co/VV9k0Hn/Kafka-Consumer-Liverliness.jpg" alt="Kafka-Consumer-Liverliness" border="0"></a>

![https://i.ibb.co/VV9k0Hn/Kafka-Consumer-Liverliness.jpg]()

Consumer in group talks to consumer group coordinator - 

To detect consumer that are down , there is heartbeat and polling mechanism

**Guideline** : To avoid issues, consumer are encouraged to process data fast and poll often; below are used to detect if consumer application is down

| property              | description                                                  |
| --------------------- | ------------------------------------------------------------ |
| session.timeout.ms    | default 10 seconds ; if no heartbeat is sent consumer is supposed to be dead |
| heartbeat.interval.ms | 1/3 of above property                                        |
| max.poll.interval.ms  | Max amount of time between two .poll() calls before declaring the consumer dead ; specially for big data frameworks -  if time is greater , consumer is declared to be dead |

Sample Snippet for Elasticsearch write and read from Kafka topic : 

```scala
import java.util.{ArrayList, List, Properties}

import com.google.gson.JsonParser
import grizzled.slf4j.Logger
import org.apache.http.HttpHost
import org.apache.http.auth.{AuthScope, UsernamePasswordCredentials}
import org.apache.http.impl.client.BasicCredentialsProvider
import org.apache.http.impl.nio.client.HttpAsyncClientBuilder
import org.apache.kafka.clients.consumer.{ConsumerConfig, ConsumerRecords, KafkaConsumer}
import org.elasticsearch.action.index.IndexRequest
import org.elasticsearch.client.{RequestOptions, RestClient, RestClientBuilder, RestHighLevelClient}
import org.elasticsearch.common.xcontent.XContentType

object elasticsearchManualCommit {

  def createClient(): RestHighLevelClient = {


    //https://vmf7wdzppa:isdodk3awd@kafkacourseabhi-3387174947.us-east-1.bonsaisearch.net ( hostname )
    // replace with your own credentials
    val hostname = "kafkacourseabhi-3387174947.us-east-1.bonsaisearch.net"; // localhost or bonsai url
    val username = "vmf7wdzppa"; // needed only for bonsai
    val password = "isdodk3awd"; // needed only for bonsai

    // credentials provider help supply username and password
    val credentialsProvider = new BasicCredentialsProvider()
    credentialsProvider.setCredentials(AuthScope.ANY,
      new UsernamePasswordCredentials(username, password));

    val builder = RestClient.builder(
      new HttpHost(hostname, 443, "https"))
      .setHttpClientConfigCallback(new RestClientBuilder.HttpClientConfigCallback() {
        override def customizeHttpClient( httpClientBuilder:HttpAsyncClientBuilder) :HttpAsyncClientBuilder ={
          return httpClientBuilder.setDefaultCredentialsProvider(credentialsProvider);
        }
      });

    val client = new RestHighLevelClient(builder);
    client
  }

  def createKafkaConsumer(topic:String) = {

    val bootStrapServer ="127.0.0.1:9092"
    val groupId = "kafak-demo-elasticSearch" // creating new group since current group is at lag 0 ( older my-fourth-app
    //val topic = "tweeter_tweets"

    val topicList: List[String] = new ArrayList[String]
    topicList.add(topic)

    val consumerProperties = new Properties()
    consumerProperties.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG,bootStrapServer)
    consumerProperties.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG,"org.apache.kafka.common.serialization.StringDeserializer")
    consumerProperties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG,"org.apache.kafka.common.serialization.StringDeserializer")
    consumerProperties.put(ConsumerConfig.GROUP_ID_CONFIG,groupId)
    consumerProperties.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG,"earliest") // or latest or none
    consumerProperties.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, "false") // to disable
    consumerProperties.put(ConsumerConfig.MAX_POLL_RECORDS_CONFIG, "10") // to disable

    val consumer = new KafkaConsumer[String, String](consumerProperties)
    consumer.subscribe(topicList)
    consumer
  }

  def main(args: Array[String]): Unit = {
    println("there we go")

    val logger = Logger[this.type]
    val client = createClient()
    val jsonString = "{ \"foo\": \"bar\" }"

    // insert data -


    val cnsr = createKafkaConsumer("tweeter_tweets")

    import scala.collection.JavaConversions._
    while(true) {
      val records: ConsumerRecords[String, String] = cnsr.poll(100) // new in kafka 2.0.0
      logger.info("Received :" + records.count())

      for (rec <- records) {
        val jsonString = rec.value()  // println("Key: " + rec.key + "  Value: " + rec.value())

        println("Partition: " + rec.partition() + "  Offset: " + rec.offset())

        // 2 strategies for creating the ID
        //val id = rec.topic() + " " + rec.partition() + " " + rec.offset();
        val id = extractIdFromTweets(rec.value())//rec.value()
        val indexRequest = new IndexRequest(
          "twitter",
          "tweets",
          id // this is make our consumer idempotent - rerunnable
        ).source(jsonString, XContentType.JSON)
        val indexResponse = client.index(indexRequest,RequestOptions.DEFAULT)
        val resp_id = indexResponse.getId()
        logger.info(resp_id)
        Thread.sleep(10)

        logger.info("committing offsets")
        cnsr.commitSync()
        logger.info("Offset Committed")
        Thread.sleep(1000)
      }
    }
    //close the client gracefully
    client.close()

    // run the process and it will output ID - which is id for insertion
    // check from elasticSearch console if data is inserted
    // id=fnCvSmgBx9_V5_s-yh59  ///twitter/tweets/fnCvSmgBx9_V5_s-yh59
    // /twitter/tweets/id

  }

  val jsonParser = new JsonParser()
  def extractIdFromTweets(tweetJson:String) = {
    //gson library
    jsonParser.parse(tweetJson).getAsJsonObject().get("id_str").getAsString()

  }

}

```

**build.gradle**

```shell
name := "kafka-consumer-elasticsearch"

version := "0.1"

scalaVersion := "2.11.12"

libraryDependencies += "org.elasticsearch.client" % "elasticsearch-rest-high-level-client" % "6.5.4"
libraryDependencies += "com.typesafe" % "config" % "1.3.2"
libraryDependencies += "org.apache.kafka" % "kafka-clients" % "1.0.0"
libraryDependencies += "org.apache.logging.log4j" % "log4j-api" % "2.11.1"
libraryDependencies += "org.apache.logging.log4j" % "log4j-core" % "2.11.1"
libraryDependencies +=  "org.apache.logging.log4j" %% "log4j-api-scala" % "11.0"


libraryDependencies ++= Seq("org.slf4j" % "slf4j-api" % "1.7.5",
  "org.slf4j" % "slf4j-simple" % "1.7.5", "org.clapper" %% "grizzled-slf4j" % "1.3.3")
libraryDependencies += "com.fasterxml.jackson.core" % "jackson-core" % "2.9.2"
libraryDependencies += "com.fasterxml.jackson.core" % "jackson-databind" % "2.9.5"

// https://mvnrepository.com/artifact/com.google.code.gson/gson
libraryDependencies += "com.google.code.gson" % "gson" % "2.8.5"

```



