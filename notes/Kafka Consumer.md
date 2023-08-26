---
tags: Kafka 
---
Consumer читает сообщение из топика. Consumer знает, из какого брокера (leader) читать сообщения (умный kafka client).

Сообщения из partition читаются в порядке добавления, FIFO. Данные из разных partition могут перемешиваться любым способом.
![[Kafka Consumer-1.png]]

## Consumer Group
Consumer Group используется для масштабирования чтения сообщений. 

## Consumer и partition offset

Kafka сохраняет offset для каждого consumer group для каждого partition - указатель на то, какое сообщение читать дальше.

Consumer должен коммитить свой ofset. Если consumer падает, то после этого он может продолжить с того сообщения, на котором упал, зная ofset. 

## Гарантии доставки (Delivery semantics)

1. **At-Most-Once (AMO):** Сообщение обрабатывается как минимум один раз, но может быть обработано несколько раз. Это может произойти, если приложение обработало сообщение, но не смогло отправить подтверждение до того, как брокер Kafka сочел сообщение успешно доставленным.
    
2. **At-Least-Once (ALO):** Сообщение гарантированно обрабатывается как минимум один раз. Это достигается путем отправки подтверждения только после успешной обработки сообщения. Однако это может привести к дублированию сообщений, если подтверждение не достигло отправителя.
    
3. **Exactly-Once (EO):** Сообщение гарантированно обрабатывается только один раз, без дублирования и потери. Это наиболее сложный режим семантики, который обеспечивает точную и надежную доставку сообщений. Для достижения точности семантики "точно один раз" в Kafka требуется взаимодействие с транзакционностью.