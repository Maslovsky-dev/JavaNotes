---
tags: ORM
--- 
Это ИНТЕРФЕЙС JPA, используемый для взаимодействия с персистентным контекстом.
EntityManager описывает API для всех основных операций над Entity, а также для получения данных и других сущностей JPA. 

Если проводить аналогию с обычным JDBC, то EntityManagerFactory будет аналогом DataSource, а EntityManager аналогом Connection. *Создание EntityManagerFactory довольно дорогая операция, поэтому обычно её создают один раз и на всё приложение.*

А чаще всего не создают сами, а **делегируют это фреймворку, такому как Spring**,
например Интерфейс Session из Hibernate представлен в JPA как раз интерфейсом EntityManager.

## Методы интерфейса EntityManager
![[методы EntityManager.png]]

## Какие функции выполняет EntityManage
**Операции над Entity**: persist (добавление Entity под управление JPA), merge (изменение), remove (удаление), refresh (обновление данных), detach (удаление из-под управления контекста персистентности), lock (блокирование Entity от изменений в других thread).

**Получение данны**х: find (поиск и получение Entity), createQuery, createNamedQuery, createNativeQuery, contains, createNamedStoredProcedureQuery, createStoredProcedureQuery.

**Получение других сущностей JPA**: getTransaction, getEntityManagerFactory, getCriteriaBuilder, getMetamodel, getDelegate.

**Работа с EntityGraph**: createEntityGraph, getEntityGraph.

**Общие операции над EntityManager или всеми Entities**: close, isOpen, getProperties, setProperty, clear.

Объекты EntityManager НЕ являются поток безопасными. Это означает, что каждый поток должен получить свой экземпляр EntityManager, поработать с ним и закрыть его в конце.
