# Getting started with Apache Kafka on OpenShift
A simple _how to_ guide for deployment of [Apache Kafka](https://kafka.apache.org/) ("Kafka") on [Red Hat OpenShift](https://www.openshift.com/container-platform/index.html) ("OpenShift").  

This guide aims to provide a basic view on:
1. What is Kafka?
2. Deploying statefull Kafka workload on OpenShift
3. Options for projects to employ Kafka on OpenShift

> This article is not meant to be a comprehensive guide but should serve as a good starting point for deploying Kafka on OpenShift

# What is Kafka?
Kafka is a distributed streaming platform.

Messaging traditionally has two models: queuing and publish-subscribe. In a queue, a pool of consumers may read from a server and each record goes to one of them; in publish-subscribe the record is broadcast to all consumers. Each of these two models has a strength and a weakness. The strength of queuing is that it allows you to divide up the processing of data over multiple consumer instances, which lets you scale your processing. Unfortunately, queues aren't multi-subscriber—once one process reads the data it's gone. Publish-subscribe allows you broadcast data to multiple processes, but has no way of scaling processing since every message goes to every subscriber.

## Why consider Kafka?
The _consumer group_ concept in Kafka generalizes these two concepts. As with a queue the consumer group allows you to divide up processing over a collection of processes (the members of the consumer group). As with publish-subscribe, Kafka allows you to broadcast messages to multiple consumer groups.

The advantage of Kafka's model is that every topic has both these properties—it can scale processing and is also multi-subscriber—there is no need to choose one or the other.

> Apache Kafka may not be suitable for all messaging use-cases.  [Paolo Patierno](https://paolopatierno.wordpress.com/github/) discusses use cases for Kafka and messaging systems like [Red Hat JBoss AMQ](https://www.redhat.com/en/technologies/jboss-middleware/amq) [here](https://www.slideshare.net/paolopat/red-hat-jboss-amq-and-apache-kafka-which-to-use).

# Deploying stateful Kafka workload on OpenShift
This section provides a step-by-step guide to deploying stateful Kafka workload on OpenShift.

1. Clone the _barnabas_ project

   ```bash
   $ git clone https://github.com/kunallimaye/barnabas
   $ cd barnabas
   ```
   
2. Ensure that using _command line tool_ you can log in to the target OpenShift cluster

   ```bash
   # In the OpenShift Web Console: (?) -> Command Line Tools -> & Copy the oc login command
   $ oc login https://ocpdemo.australiasoutheast.cloudapp.azure.com --token=<hidden>
   ```

3. Create a new project on OpenShift

   ```bash
   $ oc new-project barnabas
   ```
   
4. Import the application template into OpenShift

   ```bash
   $ oc create -f kafka-statefulsets/resources/openshift-template.yaml
   ```
   
5. Create barnabas project

   ```bash
   $ oc new-app barnabas
   --> Deploying template "barnabas/barnabas" to project barnabas

        * With parameters:
           * Zookeeper Volume Capacity=1Gi
           * Kafka Volume Capacity=1Gi
           * Repository Name=enmasseproject
           * Image Name=kafka-statefulsets
           * Kafka Version=0.11.0.0

   --> Creating resources ...
       service "kafka" created
       service "kafka-headless" created
       service "zookeeper" created
       service "zookeeper-headless" created
       statefulset "kafka" created
       statefulset "zookeeper" created
   --> Success
       Run 'oc status' to view your app.
   ```

6. Drill into in of the three (3) _kafka-N_ pods.

7. Under the _Volumes_ and _kafka_storage_ drill into the _Claim name_.  Note the _volume that is bound _the the pod.

8. Go back to the pod identified in step 6.  And using _Actions_ drop down, kill the pod.  

   > Note that the platform automatically spins up a new (identical pod) including attaching the _kafka-storage_ volume used by the original pod
   
9. Using _cli_ list _statefulsets_

   ```bash
   $ oc get statefulsets
   NAME        DESIRED   CURRENT   AGE
   kafka       3         3         9m
   zookeeper   1         1         9m
   ```

10. Scale the kafka pods to five (5) instances

    ```bash
    $ oc edit statefulset kafka
    # Under 'spec:', update the `replicas: ` to 5 instead of 3.
    # Save & Exit
    ```

    > Notice that the _kafka_ pods get scaled upto to five (5) instances

11. Test the _kafka_ infrastructure

    ```bash
    # Connect a debug session to kafka
    $ oc run -it kafka-statefulsets --rm --image=enmasseproject/kafka-statefulsets:latest --command -- bash
    
    # Within the container
    # Create a Kafka topic
    $ bin/kafka-topics.sh --create --zookeeper zookeeper --replication-factor 3 --partitions 3 --topic test
    
    # List the Kafka topics
    $ bin/kafka-topics.sh --list --zookeeper zookeeper
    
    # Produce some message on the topic
    $ bin/kafka-console-producer.sh --broker-list kafka:9092 --topic test <<EOF
    foo
    bar
    baz
    EOF
    
    # Consume the messages from the topic
    $ bin/kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic test --from-beginning
    ```
   
# Credits and external resources
This article is based on orginal works and blogs of various other contributors.  This section identifies those external resources.

Name | Description
-|-
[EnMasse Project](enmasse.io) | Open source messaging platform, with focus on scalability and performance
[Barnabas Project](https://github.com/EnMasseProject/barnabas) | Apacke Kafka as a Service on OpenShift
[Kafka on OpenShift](https://paolopatierno.wordpress.com/2017/03/25/a-new-kafka-novel-the-openshift-kubernetes-deployment/) | A very good blog by [Paolo Patierno](https://paolopatierno.wordpress.com/github/) about building from _stateless_ to _statefull_ Kafka deployments on OpenShift
[Working example](https://github.com/mattf/openshift-kafka) | A simple working example for Apache Kafka on OpenShift.  The Docker image is available on [Docker Hub](https://hub.docker.com/r/mattf/openshift-kafka/).
