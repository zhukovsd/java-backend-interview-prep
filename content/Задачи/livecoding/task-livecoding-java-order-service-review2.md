#### 71. Code Review: OrderService

🔥 Сделай ревью кода ниже. На что обратить внимание, что исправить, какие риски и улучшения возможны?

```java
public class OrderService {

@Autowired
private OrderRepository orderRepository;

@Inject
private AuditRepository auditRepository;

public void save(Order order) throws OrderValidationException {
    try {
        saveOrderInternal(order);
    } catch (Throwable e) {
        e.printStackTrace();
        throw new OrderSaveException(e, order);
    }
}

@Transactional
private void saveOrderInternal(Order order) throws OrderValidationException {
    auditRepository.save(order);
    if (order.getClient().getName() == """") {
            throw new OrderValidationException(""Поле клиент не заполнено."");
        }
        orderRepository.save(order);
    }
}

public class OrderValidationException extends Exception {
    // ...
}
```

---

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Не смешивай разные DI-аннотации: выбери `@Autowired` или `@Inject`.  
💡 Не используй `Throwable` в `catch`: лучше `Exception` или более узкий тип.  
💡 Валидацию лучше делать **до сохранения**, а не посреди транзакции.  
💡 Для строк используй `isEmpty()` или `StringUtils.isBlank()`, а не `== ""`.  
💡 Метод `saveOrderInternal` приватный, но аннотация `@Transactional` не подействует на внутренние вызовы.  
💡 `auditRepository.save(order)` до валидации — может оставить “грязные” записи.  
💡 Лучше работать с кастомными исключениями: checked vs unchecked. Обычно для бизнес-исключений делают `RuntimeException`.  
💡 Логирование через `logger.error(...)`, а не `printStackTrace()`.  
💡 Нарушение SRP: класс одновременно отвечает за валидацию, сохранение и аудит.  
{{< /details >}}

{{< details "Решение" close >}}

```java
@Service
public class OrderService {

    private final OrderRepository orderRepository;
    private final AuditRepository auditRepository;
    private final OrderValidator orderValidator;
    private static final Logger log = LoggerFactory.getLogger(OrderService.class);

    @Autowired
    public OrderService(OrderRepository orderRepository,
                        AuditRepository auditRepository,
                        OrderValidator orderValidator) {
        this.orderRepository = orderRepository;
        this.auditRepository = auditRepository;
        this.orderValidator = orderValidator;
    }

    @Transactional
    public void save(Order order) {
        try {
            orderValidator.validate(order); // валидация до сохранения

            orderRepository.save(order);
            auditRepository.save(order);

        } catch (OrderValidationException e) {
            log.warn("Ошибка валидации заказа: {}", order, e);
            throw e;
        } catch (Exception e) {
            log.error("Ошибка сохранения заказа: {}", order, e);
            throw new OrderSaveException(e, order);
        }
    }
}

@Component
public class OrderValidator {
    public void validate(Order order) throws OrderValidationException {
        if (order.getClient() == null || 
            order.getClient().getName() == null || 
            order.getClient().getName().isBlank()) {
            throw new OrderValidationException("Поле клиент не заполнено.");
        }
    }
}
```

- Валидация вынесена в отдельный класс.

- Исключения обрабатываются с логированием.

- `@Transactional` применяется на публичный метод, чтобы AOP-прокси сработал.

- `auditRepository.save(order)` выполняется **после успешного сохранения** заказа.


{{< /details >}}