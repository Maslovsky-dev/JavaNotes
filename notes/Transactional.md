---
tags: Spring
--- 

 [[Как Spring работает с транзакциями]]
`@Transactional` - это аннотация Spring Framework, которая применяется к методам или классам, чтобы определить транзакционное поведение. Эта аннотация позволяет Spring автоматически управлять транзакциями вокруг помеченных методов.

Основные атрибуты аннотации `@Transactional`:

1. **[[Propagation|propagation]]** (распространение): Определяет, как транзакция будет работать со вложенными транзакциями
    
2. **isolation** (изоляция): Определяет уровень изоляции транзакции, то есть степень ее видимости для других транзакций. Например, `Isolation.READ_COMMITTED` означает, что другие транзакции могут видеть только фиксированные изменения текущей транзакции.
    
3. **timeout** (время ожидания): Устанавливает максимальное время в секундах, которое транзакция может быть активной. Если время ожидания истекает, транзакция будет принудительно прервана.
    
4. **readOnly** (только для чтения): Указывает, что метод будет только для чтения и не будет изменять данные. Это может быть использовано для оптимизации, так как транзакция только для чтения может быть оптимизирована более эффективно.
    
5. **rollbackFor** и **noRollbackFor**: Определяют, какие исключения должны приводить к откату транзакции и какие исключения не должны приводить к откату.
    

Подробнее про isolation и propagation свойства [Baeldung](https://www.baeldung.com/spring-transactional-propagation-isolation)

>[!Warning] 
>Для того, чтобы работало декларативное управление транзакциями в Spring Boot, аннотацию @Transactional надо брать из пакета org.springframework.transaction.annotation

Пример использования `@Transactional`:
```java
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
public class ProductService {

    @Transactional
    public void updateProductPrice(Long productId, double newPrice) {
        // Логика обновления цены продукта
    }

    @Transactional(rollbackFor = {SQLException.class, CustomException.class})
    public void purchaseProduct(Long userId, Long productId) throws CustomException {
        // Логика покупки продукта
        if (someCondition) {
            throw new CustomException("Ошибка покупки продукта.");
        }
    }
}
```

В приведенном примере метод `updateProductPrice` и `purchaseProduct` помечены аннотацией `@Transactional`. Spring будет управлять транзакциями вокруг выполнения этих методов. Если метод `purchaseProduct` выбрасывает `CustomException`, транзакция будет откатываться, так как это указано в атрибуте `rollbackFor`.
 [@Transactional в Spring под капотом / Хабр](https://habr.com/ru/articles/532000/)

## Вопросы
### Как вызвать транзакционный метод из того же класса?

В теории, **@Transactional** делает метод транзакционным для этого класса и всех его наследников. На практике же, по умолчанию, если вызвать транзакционный метод **Foo.bar()**, из **Foo.baz()**, то транзакция не создастся.

Это происходит вследствие того, что по умолчанию Spring AOP добавляет код открытия и закрытия транзакции через динамический proxy класс. То есть, вместо **Foo** инджектится нечто, похожее на код на изображении.

**Первый вариант** решения проблемы – вместо аннотации использовать TransactionTemplate, то есть обернуть код в транзакцию вручную.
Примеры использования в статье: https://www.baeldung.com/spring-programmatic-transaction-management

**Другой**, более универсальный, но более сложный в конфигурации способ – переключить режим работы Spring AOP с динамических прокси на нечто другое. Обычно применяется библиотека AspectJ:
**@EnableTransactionManagement(mode = AdviceMode.ASPECTJ)**
![[Создать свой PROXY для ASPECTО.png]]
В Spring AOP есть понятие weaving – этап добавления дополнительной функциональности (аспектов).

В нашем случае, это код открытия и закрытия транзакции.

Чтобы заработал weaving AspectJ этапа компиляции, в сборку нужно добавить
плагин: aspectj-maven-plugin для maven, gradle-aspectj для gradle.

## Когда отрабатывает @Autowired а когда @Transactional? почему?
@**Autowired** работает в методе **ДО инициализации**, @**Transactional после**.

**Почему нельзя отработать @Transactional в другом методе?**
Так как оборачивает логику в прокси.
