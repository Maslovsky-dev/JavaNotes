---
tags: Kafka 
---
[Install Kafka using Docker | Learn Apache Kafka with Conduktor](https://www.conduktor.io/kafka/how-to-start-kafka-using-docker/)

В качестве простого примера используем репозиторий gitHub с готовыми настройками для запуска Kafka вместе с zooKeeper

Выполняем команду git clone https://github.com/conduktor/kafka-stack-docker-compose

Переходим в папку `kafka-stack-docker-compose`

В папке `kafka-stack-docker-compose` выполняем команду 
`docker-compose -f zk-single-kafka-single.yml up -d`

Запустится 1 zookeeper с 1 брокером

## Как выполнять команды kafka через docker
**Через docker контейнер**
`docker exec -it kafka1 bash`
Затем из контейнера можно начать выполнять  команды Kafka (без .sh)
Или открыть docker desctop нажать на 3 точки рядом с контейнером kafka и нажать open terminal.
В терминале команда чтения из топика будет выглядеть так
`kafka-console-consumer --topic firstTopic --from-beginning --bootstrap-server localhost:9092`

[[Команды Kafka]]
