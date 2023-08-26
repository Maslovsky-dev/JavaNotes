---
tags: Kafka 
---
Каждый брокер - bootstrap-server.
Каждый брокер знает обо всех других, их топиках и partitions.  
![[Kafka Broker-1.png]]

## Zookeeper
Часть кластера Kafka.
Содержит актуальный список всех брокеров топиков, partitions.