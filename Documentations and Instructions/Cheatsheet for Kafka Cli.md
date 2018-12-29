```bash
C:\Users\Swagger\Desktop\Document Storage\Technical\cmder
λ kafka-topics --zookeeper 127.0.0.1:2181 --topic first_topic --create --partitions 3 --replication-factor 2
WARNING: Due to limitations in metric names, topics with a period ('.') or underscore ('_') could collide. To avoid issues it is best to use either, but not both.
Error while executing topic command : Replication factor: 2 larger than available brokers: 1.
[2018-12-28 06:07:20,327] ERROR org.apache.kafka.common.errors.InvalidReplicationFactorException: Replication factor: 2 larger than available brokers: 1.
 (kafka.admin.TopicCommand$)

C:\Users\Swagger\Desktop\Document Storage\Technical\cmder
λ kafka-topics --zookeeper 127.0.0.1:2181 --topic first_topic --create --partitions 3 --replication-factor 1
WARNING: Due to limitations in metric names, topics with a period ('.') or underscore ('_') could collide. To avoid issues it is best to use either, but not both.
Created topic "first_topic".

C:\Users\Swagger\Desktop\Document Storage\Technical\cmder
λ kafka-topics --zookeeper 127.0.0.1:2181 --list
first_topic

C:\Users\Swagger\Desktop\Document Storage\Technical\cmder
λ kafka-topics --zookeeper 127.0.0.1:2181 --topic first_topic --describe
Topic:first_topic       PartitionCount:3        ReplicationFactor:1     Configs:
        Topic: first_topic      Partition: 0    Leader: 0       Replicas: 0     Isr: 0
        Topic: first_topic      Partition: 1    Leader: 0       Replicas: 0     Isr: 0
        Topic: first_topic      Partition: 2    Leader: 0       Replicas: 0     Isr: 0

C:\Users\Swagger\Desktop\Document Storage\Technical\cmder
λ kafka-topics --zookeeper 127.0.0.1:2181 --topic second_topic --create --partitions 6 --replication-factor 1
WARNING: Due to limitations in metric names, topics with a period ('.') or underscore ('_') could collide. To avoid issues it is best to use either, but not both.
Created topic "second_topic".

C:\Users\Swagger\Desktop\Document Storage\Technical\cmder
λ kafka-topics --zookeeper 127.0.0.1:2181 --list
first_topic
second_topic

C:\Users\Swagger\Desktop\Document Storage\Technical\cmder
λ kafka-topics --zookeeper 127.0.0.1:2181 --topic second_topic --delete
Topic second_topic is marked for deletion.
Note: This will have no impact if delete.topic.enable is not set to true.

kafka-topics --zookeeper 127.0.0.1:2181 --topic new_topic --describe

```



```powershell
zookeeper-server-start.bat config\zookeeper.properties

kafka-server-start.bat config\server.properties
```





```
kafka-console-producer.bat --broker-list 127.0.0.1:9092 --topic first_topic

kafka-console-consumer --bootstrap-server 127.0.0.1:9092 --topic first_topic --from-beginning

kafka-consumer-groups --bootstrap-server localhost:9092 --list

kafka-consumer-groups --bootstrap-server localhost:9092 --describe --group console-consumer-36825

C:\Users\Swagger\Desktop\Document Storage\Technical\cmder
λ kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic first_topic --group console-consumer-36825
and its showing up
hey adding some more
```

