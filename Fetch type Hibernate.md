---
tags: ORM
--- 
**FetchType** - Определение стратегии для извлечения данных из базы данных.  
EAGER - стратегия, которая предусматривает получение полной связи между сущностями, и последующих обращениях к связям не будет выполнять запрос на получение данных, так как данные изначально были получены полностью.  
LAZY - стратегия, которая не предусматривает получение полной связи сущностей, и при первом обращении к связи будет выполняться запрос на получение данных с БД.

Когда нужно использовать Lazy:
1.  One-to-Many or Many-to-Many Relationships: If you have entities with relationships such as one-to-many or many-to-many, and the associated collections are large or not always required, lazy loading can be beneficial. By default, Hibernate uses lazy loading for such collections, which means they are loaded on-demand when accessed. This can help improve performance by avoiding unnecessary database queries.
2.  Performance Optimization: Lazy loading can be used as a performance optimization strategy when dealing with large datasets. If you have entities with multiple associations or complex object graphs, loading all the related entities upfront may result in significant overhead. Lazy loading allows you to load only the required data when needed, reducing the initial database query and memory footprint.
3.  View or UI Rendering: If you are using your entities directly in your views or UI components, lazy loading can be useful. UI components often require only a subset of the data associated with an entity, and lazy loading helps in loading only the necessary data when rendering the UI. This can enhance the responsiveness of your application and reduce unnecessary data retrieval.

Также стоит учитывать, что hibernate по умолчанию выбирает стратегию загрузки для сущностей.
