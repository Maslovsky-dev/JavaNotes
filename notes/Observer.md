---
tags: Паттерны
---   
![](https://habrastorage.org/r/w1560/getpro/habr/upload_files/4f9/adc/57c/4f9adc57c37eb095eeea3ad525fcee2f.png)

Давайте вспомним, что это за паттерн. У нас есть подписчики и то, на что мы подписываемся. В качестве примера здесь рассмотрен Твиттер, но подписаться на какое-то сообщество или человека, а потом получать обновления можно в любой соцсети. После подписки, как только появляется новое сообщение, всем подписчикам приходит notify, то есть уведомление. Это базовый паттерн.

В данной схеме есть:

- Publisher —  тот, кто публикует новые сообщения;
- Observer —  тот, кто на них подписан. В реактивных потоках подписчик обычно называется Subscriber. Термины разные, но по сути это одно и то же. В большинстве сообществ более привычны термины Publisher/Subscriber.

Это базовая идея, на которой все строится.
