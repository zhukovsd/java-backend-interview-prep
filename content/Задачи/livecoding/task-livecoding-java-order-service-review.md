#### 70. Code Review: OrderService

🔥 Посмотри код и ответь:

- Что бы ты в нём исправил?
- По задаче: как сделал бы валидацию? Допустим, по REST’у пришла `OrderDto`. Как бы её валидировал?
- Где делать проверки на `null` параметров метода?
- Смотря на `OrderService`, что бы ты тут оставил?
- При каком сценарии транзакция в `processOrder` откатится?
- Как изменить класс, чтобы соблюдалось **SRP**?
- Что будем записывать в таблицу?
- Какие риски могут возникнуть при сохранении в БД?
- Допустим, `saveOrder` выкинул exception — что произойдет?
- В методе `updateInventory` мы увеличиваем или уменьшаем инвентарь?
- Что делает метод `decreaseStock`?
- `countByCustomerType` будет работать? Реализация сформируется?
- Где писать SQL в интерфейсе?
- Реализация `findByOrderId` сформируется?
- Какие ещё моменты выделил бы?
- Какие паттерны нарушены в этом сервисе?

```java
@Service
public class OrderService {

    @Autowired
    private OrderRepository orderRepository;

    @Autowired
    private InventoryRepository inventoryRepository;

    @Autowired
    private NotificationService notificationService;

    @Transactional
    public void processOrder(Order order) {
        validateOrder(order);
        calculateTotalPrice(order.getItems(), order.getCustomerType());
        saveOrder(order);
        notifyCustomer(order);
        updateInventory(order);
    }

    public double calculateTotalPrice(List<OrderItem> items, String customerType) {
        double total = items.stream()
                .mapToDouble(item -> item.getPrice() * item.getQuantity())
                .sum();
        if (""""VIP"""".equals(customerType)) {
            total *= 0.9;
        } else if (""""LOYALTY"""".equals(customerType)) {
            total *= 0.85;
        }

        if (orderRepository.countByCustomerType(customerType) > 10) {
            total *= 0.95; // Доп. скидка для постоянных клиентов
        }
        return total;
    }

    private void saveOrder(Order order) {
        orderRepository.save(order);
    }

    private void updateInventory(Order order) {
        order.getItems().forEach(item -> {
            inventoryRepository.decreaseStock(item.getProductId(), item.getQuantity());
        });
    }
}

@Repository
public interface OrderRepository extends JpaRepository<Order, Long> {
    int countByCustomerType(String customerType);

    Order findByOrderId(Long id);
}

```


---

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Для DTO можно использовать `javax.validation` (`@NotNull`, `@Size`, `@Positive`).  
💡 Проверки на `null` лучше делать на границах (например, в контроллере или слое валидаторов).  
💡 В `OrderService` стоит оставить только orchestration (координацию), вынеся бизнес-логику в отдельные сервисы.  
💡 Транзакция откатится, если внутри метода возникнет неперехваченное `RuntimeException` или `Error`.  
💡 SRP нарушен: класс одновременно валидирует, считает скидки, сохраняет и уведомляет. Нужно разделить.  
💡 Риски в БД: дедлоки, нарушение ограничений, частичные изменения при падении.  
💡 `decreaseStock` уменьшает количество на складе. Если нужно увеличение — другой метод.  
💡 `countByCustomerType` и `findByOrderId` корректно сгенерируются Spring Data JPA. SQL писать не нужно, но можно через `@Query`.  
💡 Нарушен паттерн **Single Responsibility** и **Separation of Concerns**. Код "толстого" сервиса.  
{{< /details >}}

{{< details "Решение" close >}}

```java
@Service
public class OrderService {

    private final OrderRepository orderRepository;
    private final InventoryService inventoryService;
    private final PricingService pricingService;
    private final NotificationService notificationService;
    private final OrderValidator orderValidator;

    @Autowired
    public OrderService(OrderRepository orderRepository,
                        InventoryService inventoryService,
                        PricingService pricingService,
                        NotificationService notificationService,
                        OrderValidator orderValidator) {
        this.orderRepository = orderRepository;
        this.inventoryService = inventoryService;
        this.pricingService = pricingService;
        this.notificationService = notificationService;
        this.orderValidator = orderValidator;
    }

    @Transactional
    public void processOrder(Order order) {
        orderValidator.validate(order);

        double totalPrice = pricingService.calculateTotalPrice(order.getItems(), order.getCustomerType());
        order.setTotal(totalPrice);

        orderRepository.save(order);

        inventoryService.decreaseInventory(order.getItems());

        notificationService.notifyCustomer(order);
    }
}
```

- `OrderValidator` отвечает за валидацию DTO (`@NotNull`, `@Size` и т. д.).

- `PricingService` считает скидки и цену.

- `InventoryService` управляет запасами.

- `NotificationService` отправляет уведомления.

- В `OrderService` осталась только orchestration логика.


{{< /details >}}
