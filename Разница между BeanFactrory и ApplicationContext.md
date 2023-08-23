---
tags: Spring
---
[Разница между BeanFactory и ApplicationContext - javascopes.com](https://javascopes.com/spring-beanfactory-vs-applicationcontext-108f29af/)
Фреймворк Spring поставляется с двумя контейнерами IOC – _BeanFactory_ и _ApplicationContext. _BeanFactory_ является самой базовой версией контейнеров IOC, а _ApplicationContext_ расширяет возможности _BeanFactory_ (транзакции добавляет?)


## Ленивая загрузка BeanFactory
_BeanFactory_ **загружает бобы по требованию, в то время как** _ApplicationContext_ **загружает все бобы при запуске**. Таким образом, _BeanFactory_ является легким по сравнению с _ApplicationContext_.

## Функции корпоративного приложения
ApplicationContext **обеспечивает** **обмен сообщениями (i18n или интернационализацию)**, **публикацию событий**, **внедрение зависимостей на основе аннотаций** и **легкую интеграцию с функциями Spring AOP**.

Кроме того, _ApplicationContext_ поддерживает почти все типы Scope bean, но _BeanFactory_ поддерживает только две области — _Singleton_ и _Prototype_. Поэтому всегда предпочтительнее использовать _ApplicationContext_ при создании сложных корпоративных приложений.

## Автоматическая регистрация BeanFactoryPostProcessor и BeanPostProcessor
_ApplicationContext_ **автоматически регистрирует** _BeanFactoryPostProcessor_ **и** _BeanPostProcessor_ при запуске. С другой стороны, _BeanFactory_ не регистрирует эти интерфейсы автоматически.
