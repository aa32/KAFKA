# Kafka 
## Kafka Installation on Ubuntu 
**step 1: Install JDK using following commands**
> check if jdk is already installed or not by executing java --version .
> if not then install

```
sudo apt-get update
sudo apt install openjdk-11-jdk
java --version
``` 
**step2: Next  download apache kafka installation file (Tar) from its website**
 ```
 mkdir Downloads
 curl https://downloads.apache.org/kafka/3.5.1/kafka_2.12-3.5.1.tgz -o Downloads/kafka.tgz
```
In the above command curl is used with -o , which means download file from the given link to output folder and save it as kafka.tgz
Pro tip: 
> curl (short for "Client URL") is a command line tool that enables data transfer over various network protocols. It communicates with > a web or application server by specifying a relevant URL and the data that need to be sent or received.
> You can use it directly on the command line or include it in a script. The most common use cases for curl are:

1. Downloading files from the internet
2. Endpoint testing
3. Debugging
4. Error logging

> Extract the contents of file in KAFKA directory:
```
mkdir KAFKA
cd KAFKA
tar -xvzf ~/Downloads/kafka.tgz --strip 1
```
Examine the contents of folders extracted ==> config, bin, libs, site-docs, licenses
inside bin directory all files are .sh files like kafka-start-server.sh and kafka-stop-server.sh.
these are executables for Linux/unix systems
There is windows directory  ==> there are .bat files => they are executables for windows systems

Inside the config there are files with ext. .properties. These are used for starting/stopping producer, consumer or broker
Inside the site-docs folder there istar file , when uncompress it will have documentation for the installed kafka version
Inside log directory 


**step3:Lets start Kafka server**
Kafka server is basically kafka broker, on every computer you can have many broker(servers running)
The pre-requisite for starting Kafka server is to start zookeeper . without zookeeper running , kafka server won't start
 **lets start zookeeper first**
```
inside the KAFKA folder:

 bin/zookeeper-server-start.sh config/zookeeper.properties
```
Now your zookeeper is running on port 2181

Once started switch the terminal and check with the following command if zookeeper is up and running:
```
telnet localhost 2181
```
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.

then type 
`srvr`
Zookeeper version: 3.6.4--d65253dcf68e9097c6e95a126463fd5fdeb4521c, built on 12/18/2022 18:10 GMT
Latency min/avg/max: 0/4.4789/55
Received: 72
Sent: 73
Connections: 2
Outstanding: 0
Zxid: 0x1d
Mode: standalone
Node count: 28

**Let's start Kafka server**
```
 bin/kafka-server-start.sh config/server.properties
```
**check server logs**
inside KAFKA folder, you will see logs directory created.
check server.log file
there you will see these msgs:

>Log directory /tmp/kafka-logs not found, creating it. (kafka.log.LogManager)
>INFO Loading logs from log dirs ArrayBuffer(/tmp/kafka-logs) (kafka.log.LogManager)
other messages which we see 
>INFO Awaiting socket connections on 0.0.0.0:9092. 
default port for kafka server =9092
>INFO [KafkaServer id=0] started
We have only one kafka server with id =0
only 1 broker with id =0

**Check the Kafka server properties**
check more logs in logs dir. check the below line in server.properties file inside config directory
>log.dirs=/tmp/kafka-logs

check for the below line . It is setby default as 1 broker with id 0 .
```
# The id of the broker. This must be set to a unique integer for each broker.
broker.id=0
```
check for other settings in this file like Topic setting, log flush policy, log retention policy and most importantly 
Socket server settings

## STEP 4   CREATE KAFKA Topic
**you can create kafka topic with bootstrap server only**
In old editions, it was possible to create kafka topic with zookeeper server also, but that is deprecated

```
bin/kafka-topics.sh --create --bootstrap-server localhost:9092 --topic cities
```
Execute the below command for description of the created topic :
```
bin/kafka-topics.sh --describe --bootstrap-server localhost:9092
```
In output you get following:
> Topic :   cities
> TopicId : 
> PartitionCount: 1
> ReplicationFactor :1
>Configs: Topic: cities   Partition: 0    Leader: 0       Replicas: 0     Isr: 0

since there is only 1 partition, it starts with id 0 , this is the Leader also 
If there are 2 partitions , it will be 0 and 1 etc.
**Here Isr stands for In-Sync Replica: Number of Replicas which are in-sync with the leader.**

## STEP 5: Send and consume events
Inside KAFKA/bin folder these two scripts have to be executed for sending and consuming messages/events:
1. bin/kafka-console-consumer.sh
2. bin/kafka-console-producer.sh

Execute this command to produce messages in a new terminal:
```
bin/kafka-console-producer.sh --bootstrap-server localhost:9092 --topic cities
>New York
>California
>Paris
>London
>Delhi
>Mumbai
>Kolkata
```
Open other terminal window tab and 
Execute this command to start the consumer
```
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic cities
```
go to terminal window where producer is running and give few other city names like Dubai,San Francisco
and when you switch to consumer terminal , you will see these cities have been consumed by consumer.
**But what about the cities we entered before???**
Execute below command to see:
```
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic cities --from-beginning
```
You will see all the cities enetered even when consumer was not started. that means :
**Conclusion: Kafka stores the messages to be picked by consumer later**

**Let us create multiple consumers**
Open separate terminal window and start one more consumer listening to topic:cities using exactly same command as we did before:
Now go to the terminal window where Producer was running and enter new city say Agra
and then check the terminal windows of both the consumers and you see that they both consumed the new message 
**Conclusion: Multiple consumers can listen to single topic**

**Let us create multiple producers**
Open separate terminal window and start one more Producer , sending events/msgs to topic:cities using exactly same command as we did before:
Now go to the terminal window where new Producer is strated and enter new city say "BARCELONA"
and then check the terminal windows of both the consumers and you see that they both consumed the new message .
**Conclusion: multiple  producers can produce to single topic**


    






