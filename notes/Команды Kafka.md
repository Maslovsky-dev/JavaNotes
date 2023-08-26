---
tags: Kafka 
---
1. **Создание топика:** Создать новый топик с определенным именем, количеством разделов и фактором репликации.
    
    `kafka-topics --create --topic mytopic --bootstrap-server localhost:9092 --partitions 3 --replication-factor 2`
    
2. **Список топиков:** Посмотреть список всех доступных топиков в кластере Kafka.

    `kafka-topics --list --bootstrap-server localhost:9092`
    
3. **Описание топика:** Получить информацию о конфигурации и структуре определенного топика.

    `kafka-topics --describe --topic mytopic --bootstrap-server localhost:9092`
    
4. **Отправка сообщения:** Отправить сообщение в определенный топик.
    
    `kafka-console-producer --topic mytopic --bootstrap-server localhost:9092`
    
    После запуска этой команды вы сможете вводить сообщения в консоли, и они будут отправляться в указанный топик.
    
5. **Потребление сообщений:** Потребить сообщения из определенного топика.
    
    `kafka-console-consumer --topic mytopic --bootstrap-server localhost:9092 --from-beginning`
    
    Эта команда начнет выводить сообщения из указанного топика с самого начала.