Log4j - это Java-библиотека для логирования сообщений из приложения. Она позволяет настраивать вывод сообщений в различные места (консоль, файлы, базы данных и т.д.) и управлять уровнем детализации логов.

В Spring Boot можно использовать Log4j2 для логирования. Вот несколько статей на эту тему:

## Терминология
- **Logger:** Логгер - это объект, который отвечает за генерацию сообщений журнала. Логгеры могут быть организованы по иерархической структуре, и каждый логгер может иметь свой собственный уровень логирования.
- **Уровень логирования:** Уровень логирования определяет, какие сообщения журнала должны быть выведены. Существует ряд уровней логирования, в том числе TRACE, DEBUG, INFO, WARN, ERROR и FATAL.
- **Appender:** - это объект, который отвечает за запись сообщений журнала в целевой файл или поток. Существует ряд аппендеров, в том числе файл аппендеры, консольные аппендеры и удаленные аппендеры.
	- SOCKET сокет аппендер - вид аппендера, который отправляет сообщения журнала по сети на удаленный сервер
- **Layout:**  - это объект, который отвечает за форматирование сообщений журнала. Существует ряд лейаутов, в том числе простой лейаут, расширенный лейаут и XML лейаут.
- **Configuration:** Конфигурация - это файл, который содержит параметры логирования для вашего приложения. Конфигурация может быть в формате XML или Java.

	Logstash - это инструмент сбора, обработки и хранения журналов с открытым исходным кодом. Он используется для сбора журналов из различных источников, таких как файлы, сокеты, API и т. д., а затем обработки и хранения этих журналов в различных форматах, таких как CSV, JSON и XML. Logstash также может использоваться для поиска и анализа журналов.
## Базовое логирование
[Удобное логирование в SpringBoot + Log4j2 + Maven / Хабр](https://habr.com/ru/articles/413091/)
### Конфиг приложения
В первую очередь внесём изменения в конфигурацию Maven (pom.xml):
В переменные на уровне всего pom.xml добавляем адрес host для logstash-appender:
```xml
<properties>
    <logstash.host>logstashcsm.example.ru</logstash.host>
</properties>
```

Добавляем необходимые для работы зависимости:

```xml
<dependencies>    
    <dependency>    
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <exclusions>
            <exclusion>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-logging</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-log4j2</artifactId>
    </dependency>
</dependencies>
```

Обратите внимание, что из spring-boot-starter-web мы исключаем зависимость spring-boot-starter-logging.

Настроим Maven-профили, чтобы динамически управлять подключенными appender'ами. На уровне каждого профиля задаем переменные `logstash.port, logger.console.tresholdFilter, logger.socket.tresholdFilter`.

```xml
<profiles>
    <profile>
        <id>local</id>
        <properties>
            <logstash.port>10000</logstash.port>
            <logger.console.tresholdFilter>ALL</logger.console.tresholdFilter>
            <logger.socket.tresholdFilter>OFF</logger.socket.tresholdFilter>
        </properties>
    </profile>

    <profile>
        <id>dev</id>
        <properties>
            <logstash.port>10001</logstash.port>
            <logger.console.tresholdFilter>OFF</logger.console.tresholdFilter>
            <logger.socket.tresholdFilter>ALL</logger.socket.tresholdFilter>
        </properties>
    </profile>
</profiles>
```

**logstash.port** — порт, на который нужно отправлять логи.
**logger.console.tresholdFilter** — значение задаёт уровень фильтрации логов, выводимых на консоль. В нашем случае ALL означает, что лог-записи всех уровней будут выводиться в консольный аппендер.
**logger.socket.tresholdFilter** — значение задает уровень фильтрации логов, которые отправляются в logstash. OFF — означает, что никакие записи отправлены в этот аппендер не пройдут.

Теперь нам необходимо внести изменения в application.properties, чтобы из файла Log4j2.xml можно было получить доступ к значению переменных, указанных в pom.xml:

`logstash.host=@logstash.host@`
`logstash.port=@logstash.port@`
`logger.console.tresholdFilter=@logger.console.tresholdFilter@`
`logger.socket.tresholdFilter=@logger.socket.tresholdFilter@`

И, наконец, настраиваем конфигурацию самого Log4j2 в файле log4j2.xml:

### Конфиг Log4j
```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
    <Properties>
        <Property name="socket.host">${bundle:application:logstash.host}</Property>
        <Property name="socket.port">${bundle:application:logstash.port}</Property>
        <Property name="console.thresholdFilter">${bundle:application:logger.console.tresholdFilter}</Property>
        <Property name="socket.thresholdFilter">${bundle:application:logger.socket.tresholdFilter}</Property>
    </Properties>
    <Appenders>
        <Console name="CONSOLE" target="SYSTEM_OUT">
            <ThresholdFilter level="${console.thresholdFilter}"/>
            <PatternLayout pattern="%d %-5p [%t] %c{10} - %m%n"/>
        </Console>
        <Socket name="SOCKET" host="${socket.host}" port="${socket.port}" immediateFlush="true">
            <ThresholdFilter level="${socket.thresholdFilter}"/>
            <JSONLayout eventEol="true" compact="true"/>
        </Socket>
        <Async name="ASYNC">
            <AppenderRef ref="CONSOLE"/>
            <AppenderRef ref="SOCKET"/>
        </Async>
    </Appenders>

    <Loggers>
        <Logger name="ru.example" level="debug" additivity="false">
            <AppenderRef ref="ASYNC"/>
        </Logger>
        <Root level="error">
            <AppenderRef ref="ASYNC"/>
        </Root>
    </Loggers>
</Configuration>
```

### Запись в лог
Теперь, чтобы вызвать логгер в своём классе необходимо создать его экземпляр:

```java
private static Logger logger = LoggerFactory.getLogger(YourClass.class);
```

и обратиться к нему:

```java
logger.info("Эта запись будет залогирована");
```


Вот и всё. Теперь логирование будет работать с учетом Maven-профиля, активного в проекте.
Обратите внимание, что в листинге файла `log4j2.xml` определено два логгера — `ru.example` и `root`, и у них разные уровни логирования событий. Первый будет работать на все события, порожденные классами из пакета `ru.example.*` и логгировать всё от уровня DEBUG, а `root` логгер будет фиксировать события из абсолютно всех классов, но с уровня ERROR.
При этом, чтобы записи не дублировались в логах, используется настройка `additivity="false"`.
