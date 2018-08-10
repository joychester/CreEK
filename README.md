# CreEK
C: Confluent, E: Elasticsearch, K: Kibana

How a CreEK is formed: 
> * Send Messages by Kafka REST Proxy --> Kafka Topics --> Kafka Elasticsearch (Sink) Connector --> Elasticsearch --> Kibana

## Start Confluent services:

* (optional) Modify ```/etc/schema-registry/connect-avro-distributed.properties``` if want to disable avro schema validation and use plain JSON data, check out the sample file in the repo
* Create kafka connect properties file: for example, ```/etc/kafka-connect-elasticsearch/elastic-connect-test.properties```, check out the sample file in the repo
* Start Confluent services:  ```./confluent start```
* Check Confluent services status to make sure services are all started: ```./confluent status```
* Start Elasticsearch service: 
> * you may want to edit jvm.options to increase the JVM heap size
> * run ```./elasticsearch -d```
* Start Kibana service: 
> * edit config/kibana.yml => server.host: "0.0.0.0"
> * add $NODE_OPTIONS --max-old-space-size=2048 to NODE_ENV in bin/kibana file
> * run ```nohup ./kibana &```
* Create Kafka Topics: ```./kafka-topics --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic my_first_topic```
* (optional) Produce and Consume the message to valiadte the topic been created
* Load Kafka elasticsearch connector: for example, ```./confluent load elastic-connect-test -d ../etc/kafka-connect-elasticsearch/elastic-connect-test.properties```
* Check Kafak elasticsearch connector status: ```curl localhost:8083/connectors/elastic-connect-test/status``` , if there is anything wrong with the connector, you have to delete the connector first and recreated the connector: ```curl -X DELETE localhost:8083/connectors/elastic-connect-test```
* Start to Sink your data to Kafka Topics(Jenkins + JMeter in my case), and the Data will move to right away, manage your index through Kibana directly
* Create create visualizations of the data and make your own Dashboards in Kibana


------
#### PS. Inside of ./confluent start CLI => The equivalent commands to start every service in its own terminal:
> * Start ZooKeeper.  Run this command in its own terminal.
>>> $ ./bin/zookeeper-server-start ./etc/kafka/zookeeper.properties
> * Start Kafka.  Run this command in its own terminal.
>>> $ ./bin/kafka-server-start ./etc/kafka/server.properties
> * Start Schema Registry. Run this command in its own terminal.
>>> $ ./bin/schema-registry-start ./etc/schema-registry/schema-registry.properties
> * Start Connect in distributed mode. Run this command in its own terminal.
>>> $ ./bin/connect-distributed ./etc/schema-registry/connect-avro-distributed.properties
