---
tags: Java 
--- 
Класс RestTemplate это специальный клиент в Spring для отправки http-запросов (что-то вроде встроенной программы POSTMAN). Он предоставляет удобные API для легкого вызова конечных точек (эндпойнтов) REST'а в одну строку.  

``` java
RestTemplate restTemplate = new RestTemplate();  
String fooResourceUrl = "http://localhost:8080/spring-rest/foos";  
ResponseEntity response = restTemplate.getForEntity(fooResourceUrl + "/1",  
String.class);  
```
Методы:  
1. delete () Удалить ресурсы по указанному URI.  
2. exchange () Выполнить определенный HTTP-метод для URL-адреса и вернуть ResponseEntity,  содержащий объект, который взят из тела ответа  
3. execute () Выполнить определенный HTTP-метод для URL-адреса и вернуть объект,  отображенный из тела ответа  
4. doExecute() Выполните данный метод на предоставленном URI.  
5. getForEntity () отправляет HTTP-запрос GET, а возвращенный ResponseEntity содержит объект, отображенный в теле ответа  
6. postForEntity () POST-данные в URL-адрес и возвращение ResponseEntity, содержащего объект, который отображается из тела ответа  
7. getForObject () отправляет HTTP-запрос GET, и возвращаемое тело запроса будет сопоставлено с объектом  
8. postForObject () Отправьте данные POST в URL и верните объект, сформированный в соответствии с соответствием тела ответа  
9. patchForObject () Обновите ресурс, вставив данный объект в шаблон URI, и верните представление, найденное в ответе.  
10. headForHeaders () отправляет HTTP-запрос HEAD и возвращает HTTP-заголовок,  
содержащий URL-адрес определенного ресурса.  
11. optionsForAllow () отправляет запрос HTTP OPTIONS и возвращает информацию заголовка  Allow для определенного URL  
12. postForLocation () POST-данные в URL-адрес и возвращение URL-адреса вновь созданного ресурса  
13. put () Создайте или обновите ресурс, поместив данный объект в URI.  
14. httpEntityCallback () Возвратите RequestCallback реализацию, которая записывает данный объект в поток запроса.  
15. acceptHeaderRequestCallback() Возвращает RequestCallback, который устанавливает Accept заголовок запроса на основе заданного типа ответа, перекрестно сверяясь с настроенными преобразователями сообщений.  
16. handleResponse () Обработайте данный ответ, выполняя соответствующую регистрацию и вызывая, ResponseErrorHandler если необходимо.  
17. getErrorHandler() Возвращает обработчик ошибок  
18. setErrorHandler() Установите обработчик ошибок.  
19. setDefaultUriVariables() Настройте значения переменных URI по умолчанию.  
20. headersExtractor() Вернуть экстрактор ответа для HttpHeaders.  
21. responseEntityExtractor() Вернуть a ResponseExtractor, который готовит файл ResponseEntity.  
22. getUriTemplateHandler() Возврат настроенного обработчика шаблона URI.  
23. setUriTemplateHandler() Настройте стратегию расширения шаблонов URI.  
24. getMessageConverters() Возвращает список преобразователей тела сообщения.  
25. setMessageConverters() Установить преобразователи тела сообщения для использования
