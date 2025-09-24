#### 73. Исправление ошибок: OrderService

🔥 Исправление ошибок в коде

```java
public class OrderService {

@Autowired
private OrderRepository orderRepository;

private final NotificationService notificationService;

@Autowired
public OrderService(NotificationService notificationService) {
    this.notificationService = notificationService;
}

public void updateOrderStatus(Long orderId, String newStatus) {
    Order order = orderRepository.findById(orderId);

    if (newStatus.equals(""COMPLETED"")) {
        order.setStatus(""COMPLETED"");
        notificationService.notify(order.getUserId(), ""Your order is completed"");
    } else if (newStatus.equals(""CANCELLED"")) {
        order.setStatus(""CANCELLED"");
        notificationService.notify(order.getUserId(), ""Your order is cancelled"");
    } else if (newStatus.equals(""PENDING"")) {
        order.setStatus(""PENDING"");
        orderRepository.save(order);
    } else if (newStatus.equals(""IN_PROGRESS"")) {
        order.setStatus(""IN_PROGRESS"");
        orderRepository.save(order);
    } else {
        throw new IllegalArgumentException(""Unsupported status: "" + newStatus);
    }
}
}
        
```

---

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Используй **один стиль DI** — конструктор для всех зависимостей (`final` поля, `@Autowired` на конструкторе).  
💡 `findById` в Spring Data возвращает `Optional`: обработай случай, когда заказа нет.  
💡 Проверяй `orderId` и `newStatus` на `null/blank`, избегай `newStatus.equals(...)` → возможен NPE.  
💡 Статусы как **enum**, а не строковые литералы. Парси через `valueOf` безопасно.  
💡 Сохраняй заказ **после** изменения статуса во всех ветках, а уведомления — **после успешного сохранения**.  
💡 Оберни метод в `@Transactional`, чтобы изменение и сохранение были атомарны.  
💡 Сделай обновление идемпотентным: если статус не поменялся — ничего не делай.  
💡 Логируй, не используй `printStackTrace()`.  
{{< /details >}}

{{< details "Решение" close >}}

```java
@Service
public class OrderService {

    private final OrderRepository orderRepository;
    private final NotificationService notificationService;
    private static final Logger log = LoggerFactory.getLogger(OrderService.class);

    @Autowired
    public OrderService(OrderRepository orderRepository,
                        NotificationService notificationService) {
        this.orderRepository = Objects.requireNonNull(orderRepository);
        this.notificationService = Objects.requireNonNull(notificationService);
    }

    @Transactional
    public void updateOrderStatus(Long orderId, String newStatus) {
        if (orderId == null) {
            throw new IllegalArgumentException("orderId must not be null");
        }
        if (newStatus == null || newStatus.isBlank()) {
            throw new IllegalArgumentException("newStatus must not be null or blank");
        }

        Order order = orderRepository.findById(orderId)
                .orElseThrow(() -> new OrderNotFoundException(orderId));

        OrderStatus targetStatus = parseStatus(newStatus);

        // идемпотентность
        if (targetStatus == order.getStatus()) {
            log.debug("Status is already {} for orderId={}", targetStatus, orderId);
            return;
        }

        order.setStatus(targetStatus);
        orderRepository.save(order); // сохранить во всех кейсах

        // уведомления после успешного сохранения
        if (targetStatus == OrderStatus.COMPLETED) {
            notificationService.notify(order.getUserId(), "Your order is completed");
        } else if (targetStatus == OrderStatus.CANCELLED) {
            notificationService.notify(order.getUserId(), "Your order is cancelled");
        }

        log.info("Order {} status changed to {}", orderId, targetStatus);
    }

    private OrderStatus parseStatus(String status) {
        try {
            return OrderStatus.valueOf(status.trim().toUpperCase(Locale.ROOT));
        } catch (IllegalArgumentException ex) {
            throw new IllegalArgumentException("Unsupported status: " + status);
        }
    }
}

// Доменные типы и исключения

public enum OrderStatus {
    PENDING, IN_PROGRESS, COMPLETED, CANCELLED
}

public class OrderNotFoundException extends RuntimeException {
    public OrderNotFoundException(Long id) {
        super("Order not found: " + id);
    }
}
```

- Конструкторная инъекция и `final` поля.
- Безопасный парсинг статуса в `enum`.
- `@Transactional` гарантирует атомарность.
- Сохранение происходит для **всех** статусов.
- Уведомления отправляются только для важных переходов и **после** сохранения.
- Обработка отсутствующего заказа и защита от `null/blank`.

{{< /details >}}