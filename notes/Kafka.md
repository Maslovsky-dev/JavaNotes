---
tags: Kafka 
---

Apache Kafka - это распределенная платформа, предназначенная для создания, передачи и обработки данных в реальном времени. Она была изначально разработана компанией LinkedIn и затем передана в проект с открытым исходным кодом Apache Software Foundation.

Основные понятия в Kafka:

1. **Брокеры (Brokers)**: Серверы, на которых запущен Apache Kafka. Они обрабатывают входящие и исходящие данные.
    
2. **Топики (Topics)**: Категории, в которых хранятся исходные данные. Они могут быть разделены на различные разделы, называемые "партициями".
    
3. **Партиции (Partitions)**: Каждый топик разбивается на несколько партиций, что позволяет распределить нагрузку и обеспечить масштабируемость.
    
4. **Производители (Producers)**: Приложения, которые записывают данные в топики Kafka.
    
5. **Потребители (Consumers)**: Приложения, которые считывают данные из топиков Kafka. Потребители могут читать данные из параллельных партиций для достижения высокой пропускной способности.
    
6. **Потоковая обработка (Stream Processing)**: Kafka Streams - библиотека для разработки приложений, которые могут анализировать и обрабатывать данные в реальном времени, используя данные из Kafka.
    

Apache Kafka имеет множество применений, таких как запись журналов, потоковая обработка, агрегация данных, мониторинг и многое другое. Она широко используется в сценариях, требующих высокой производительности и надежности при обработке и передаче больших объемов данных в реальном времени.