---
tags: ORM
--- 
Java Persistence API (JPA) использует интерфейс javax.persistence.**EntityManager** для управления экземплярами **Entity** и их жизненным циклом (Hibernate делает это с помощью интерфейса org.hibernate.**Session**).

Каждый экземпляр EntityManager связан с Persistence Context, который является своего рода кэшем данных в рамках транзакции — это и есть **кэш первого уровня**.
![[Persistence context.png|300]]
Persistence контекст бывает двух типов в зависимости от этапа жизненного цикла: 
- Persistence контекст области транзакции (Transaction scope persistence context) 
- Расширенный persistence контекст (Extended persistence context)
	Для EntityManager, управляемого контейнером, можно выбрать тип persistence контекста. Укажите тип контекста в атрибуте type @PersistenceContext. Типом по умолчанию является Transaction scope. Обратите внимание, что расширенный контекст всегда используется для EntityManager, управляемого приложением (в этом случае нельзя выбирать тип контекста).
