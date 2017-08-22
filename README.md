# Getting started with Apache Kafka on OpenShift
A simple _how to_ guide for deployment of [Apache Kafka](https://kafka.apache.org/) ("Kafka") on [Red Hat OpenShift](https://www.openshift.com/container-platform/index.html) ("OpenShift").  

This guide aims to provide a basic view on:
1. What is Kafka?
2. Deploying stateless Kafka workload on OpenShift
3. Deploying statefull Kafka workload on OpenShift
4. Options for projects to employ Kafka on OpenShift

> This article is not meant to be a comprehensive guide but should serve as a good starting point for deploying Kafka on OpenShift

# What is Kafka?

Kafka is a distributed streaming platform.

Messaging traditionally has two models: queuing and publish-subscribe. In a queue, a pool of consumers may read from a server and each record goes to one of them; in publish-subscribe the record is broadcast to all consumers. Each of these two models has a strength and a weakness. The strength of queuing is that it allows you to divide up the processing of data over multiple consumer instances, which lets you scale your processing. Unfortunately, queues aren't multi-subscriber—once one process reads the data it's gone. Publish-subscribe allows you broadcast data to multiple processes, but has no way of scaling processing since every message goes to every subscriber.

## Why consider Kafka?
The _consumer group_ concept in Kafka generalizes these two concepts. As with a queue the consumer group allows you to divide up processing over a collection of processes (the members of the consumer group). As with publish-subscribe, Kafka allows you to broadcast messages to multiple consumer groups.

The advantage of Kafka's model is that every topic has both these properties—it can scale processing and is also multi-subscriber—there is no need to choose one or the other.

> Apache Kafka may not be suitable for all messaging use-cases.  [Paolo Patierno](https://paolopatierno.wordpress.com/github/) discusses use cases for Kafka and messaging systems like [Red Hat JBoss AMQ](https://www.redhat.com/en/technologies/jboss-middleware/amq) [here](https://www.slideshare.net/paolopat/red-hat-jboss-amq-and-apache-kafka-which-to-use).

# Credits and external resources
This article is based on orginal works and blogs of various other contributors.  This section identifies those external resources.

Name | Description
-|-
[EnMasse Project](enmasse.io) | Open source messaging platform, with focus on scalability and performance
[Barnabas Project](https://github.com/EnMasseProject/barnabas) | Apacke Kafka as a Service on OpenShift
[Kafka on OpenShift](https://paolopatierno.wordpress.com/2017/03/25/a-new-kafka-novel-the-openshift-kubernetes-deployment/) | A very good blog by [Paolo Patierno](https://paolopatierno.wordpress.com/github/) about building from _stateless_ to _statefull_ Kafka deployments on OpenShift
[Working example](https://github.com/mattf/openshift-kafka) | A simple working example for Apache Kafka on OpenShift.  The Docker image is available on [Docker Hub](https://hub.docker.com/r/mattf/openshift-kafka/).
