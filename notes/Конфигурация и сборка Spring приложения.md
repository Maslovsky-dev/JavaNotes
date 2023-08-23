---
tags: Spring
--- 
## Конфигурационный файл
Настройки конфигурации Spring приложения хранятся в файле application.properties. Этот файл содержит данные в формате "ключ" "значение". Доступ к этому файлу можно получить через переменную среды enironment env.getProperty(propName
)
Пример:
```
## MySQL properties  
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver  
spring.datasource.url=jdbc:mysql://localhost:3306/security_bootstrap?allowPublicKeyRetrieval=true  
spring.datasource.username=DB_root  
spring.datasource.password=root  
  
## Hibernate properties  
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQLDialect  
spring.jpa.properties.hibernate.show_sql=true  
spring.jpa.properties.hibernate.hbm2ddl.auto=validate  
  
## Enable HTTP methods  
spring.mvc.hiddenmethod.filter.enabled=true
```
