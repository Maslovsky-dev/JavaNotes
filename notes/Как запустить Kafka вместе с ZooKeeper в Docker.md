---
tags: Kafka 
---
Создаем docker-compose.yml файл 
```yml
version: '3'
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:6.2.0
    ports:
      - "2181:2181"
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000


  kafka:
    image: confluentinc/cp-kafka:6.2.0
    ports:
      - "9092:9092"
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_LISTENERS: PLAINTEXT://:9092
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_AUTO_CREATE_TOPICS_ENABLE: "true"
```

Запускаем контейнеры командой
`docker-compose up -d`

## Для запуска командной строки kafka

1. Запустите команду `docker ps` для того, чтобы узнать ID или имя контейнера, запустившего Kafka. Найдите имя или ID контейнера, связанного с Kafka. Обычно он будет иметь имя в формате `yourproject_kafka_1`.
    
2. Запустите интерактивную оболочку внутри контейнера Kafka с помощью команды `docker exec -it CONTAINER_ID_OR_NAME bash`, где `CONTAINER_ID_OR_NAME` - это ID или имя контейнера Kafka.
    
3. Внутри контейнера выполните команду для создания топика. Например, чтобы создать топик с именем `mytopic`, выполните следующую команду:
    

```
kafka-topics --create --topic mytopic --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1
```

Здесь `--bootstrap-server` указывает адрес и порт брокера Kafka, в данном случае, `localhost:9092`.

4. Выходите из интерактивной оболочки контейнера с помощью команды `exit`.

После выполнения этих шагов, у вас должен быть создан новый топик `mytopic` в вашем кластере Kafka.

[[Команды Kafka]]
