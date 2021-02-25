## Streams Architecture

Kafka Streams simplifies application development by building on the Apache Kafka producer and consumer APIs, and leveraging the native capabilities of Kafka to offer data parallelism, distributed coordination, fault tolerance, and operational simplicity.

 - Kafka Streams is a client library for stream processing applications that process records in kafka topics.
 - Kafka streams provides the high level DSL like KStream and KTable.
 - Kafka streams provide the wrapper around the kafka Producer and Consumer API's.
 - Kafka streams provide the Topology to describe the flow of the stream.
 - In Kafka streams the Topology process one record at a time.
 - Kafka streams application can be run in multiple instances. 

## Core Concepts:

 

 - **Stream :** A Stream is a sequence of immutable data records that are fully ordered, can be restart and fault tolerant.
 - **Stream Processor** : A Stream Processor defines the stream processing computational logic for your application how input data is transformed into output data. A topology is a graph of [stream processors](https://docs.confluent.io/platform/current/streams/concepts.html#streams-concepts-processor) (nodes) that are connected by [streams](https://docs.confluent.io/platform/current/streams/concepts.html#streams-concepts-stream) (edges) or shared [state stores](https://docs.confluent.io/platform/current/streams/architecture.html#streams-architecture-state). There are two special processors in the topology:

<!--stackedit_data:
eyJoaXN0b3J5IjpbMzA4MzI1NDQ3LC02NzYyMTM5NjYsLTEwOD
gyMTQ1NTQsLTExMTM1NjM4MjYsLTE5NDQ2Nzc0NDAsMTY3Mjg4
MzczMSwtNzQ1NTg0NzEzLC02NDcyOTk2NzgsNDA4MjAzNDg2LC
0xOTQ4NDUzOTY1LDY2MzUzNDg2OCwzNjA0ODA2ODAsMTAxODEw
MDIxMywxNTYyNzc1NTY3LDU0NTExNjMyMywxNjkzMzg5NjU5LC
0zNTkxNDUzNTksNDc2NDM1MDQ3LC0xMTc1NTM2ODc5LDYyOTgw
Mjc3M119
-->