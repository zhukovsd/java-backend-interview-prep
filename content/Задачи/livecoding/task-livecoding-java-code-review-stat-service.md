#### 99. Code review сервиса подсчёта статистики по заказам клиента

**Условие задачи:**  
📌 Дан сервис, который собирает статистику по заказам пользователей. Нужно провести code review: что ок, что не ок, и как бы ты исправил.

**Код:**

```java
@Component
@RequiredArgsConstructor
public class StatService {

    @Autowired
    private UserRepository userRepository;
    @Autowired
    private OrderRepository orderRepository;
    @Autowired
    private StatRepository statRepository;
    @Autowired
    private KafkaService kafkaService;

    public void calculate() {
        List<User> users = userRepository.findAll().stream().filter(u -> u.isActive()).collect(Collectors.toList());

        if (!users.isEmpty()) {
            for(User user : users) {
                List<Order> orders = orderRepository.findByUser(user.getId());

                if (orders.isEmpty()) {
                    System.out.println("У пользователя нет заказов");
                }

                int totalOrders = 0;
                int totalCost = 0;
                for(Order order : orders) {
                    totalOrders++;
                    totalCost += order.getCost();
                }

                save(user.getId(), totalOrders, totalCost);
            }
        }
    }
@Transactional
private void save(Long userId, int totalOrders, int totalCost) {
    kafkaService.send(userId, totalOrders, totalCost, Instant.now());
}
```

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Нельзя одновременно `@RequiredArgsConstructor` и field injection `@Autowired` — выбери одно (лучше constructor injection).  
💡 `findAll()` и фильтрация в памяти — плохо для производительности, лучше `findAllByActiveTrue()` или query.  
💡 N+1 проблема: на каждого пользователя отдельный запрос `findByUser(...)`. Лучше агрегировать на уровне БД.  
💡 `@Transactional` на `private` методе не сработает (self-invocation).  
💡 Метод `save` не сохраняет в `statRepository`, а только шлёт в Kafka — название вводит в заблуждение.  
💡 `System.out.println` → заменить на логгер.  
💡 Сумма стоимости в `int` может переполниться, деньги лучше `BigDecimal`/`long` (копейки).  
💡 `Instant.now()` лучше вычислять один раз на запуск/пользователя и передавать явно.  
{{< /details >}}

{{< details "Решение" close >}}

### Что ок

- ✅ Идея разделить «расчёт» и «побочные эффекты» (отправка/сохранение) правильная.

- ✅ Используются репозитории, сервис изолирует бизнес-логику.


### Что не ок и почему

1. **DI сломан по стилю**


- `@RequiredArgsConstructor` предполагает `final` поля и конструкторную инъекцию.

- Но поля не `final` и размечены `@Autowired` → смешение подходов.


✅ Исправить: сделать `final` поля и убрать `@Autowired`.

2. **Плохая выборка пользователей**


```java
userRepository.findAll().stream().filter(u -> u.isActive())
```

- тянем всех пользователей из БД, фильтруем в памяти.


✅ Исправить: репозиторий должен уметь `findAllByActiveTrue()`.

3. **N+1 запросов**


- На каждого пользователя отдельный запрос по заказам → при 100k пользователей будет катастрофа.


✅ Исправить: считать статистику агрегирующим SQL:

- `SELECT user_id, COUNT(*), SUM(cost) FROM orders WHERE user_id IN (...) GROUP BY user_id`  
  или прямо по активным пользователям join-ом.


4. **@Transactional на private-методе**


- `@Transactional` через прокси.

- `private save()` + вызов из того же класса → транзакция НЕ включится.


✅ Исправить:

- либо транзакцию на публичный entrypoint `calculate`,

- либо вынести отправку/сохранение в отдельный бин.


5. **Название метода save() неверное**


- Он ничего не сохраняет, только шлёт в Kafka.


✅ Переименовать в `publishStat(...)` / `sendStat(...)`.

6. **System.out.println**


- не логируется нормально, нет уровней, невозможно управлять.


✅ Логгер.

7. **Деньги в int**


- `totalCost` может переполниться, а деньги нельзя хранить в `int`.


✅ `long` (копейки) или `BigDecimal`.

---

### Пример исправленного варианта (с агрегацией в БД)

```java
@Service
@RequiredArgsConstructor
public class StatService {

    private final UserRepository userRepository;
    private final OrderRepository orderRepository;
    private final KafkaService kafkaService;

    public void calculate() {
        List<Long> activeUserIds = userRepository.findAllActiveIds(); // запросом, не в память всех
        if (activeUserIds.isEmpty()) return;

        Instant now = Instant.now();

        // агрегируем в БД: userId -> (count, sum)
        List<UserOrderStat> stats = orderRepository.aggregateStatsByUserIds(activeUserIds);

        // если нужно отправлять и тем, у кого 0 заказов — можно дополнить нулями
        for (UserOrderStat s : stats) {
            kafkaService.send(s.userId(), s.totalOrders(), s.totalCost(), now);
        }
    }

    // удобная проекция
    public record UserOrderStat(long userId, int totalOrders, long totalCost) {}
}
```

**Что нужно в репозиториях (примерно):**

- `UserRepository.findAllActiveIds()`

- `OrderRepository.aggregateStatsByUserIds(...)`


---

### Если транзакция реально нужна

Если помимо Kafka надо писать в БД (`statRepository.save(...)`), тогда:

- транзакцию ставим на публичный метод, который делает запись в БД,

- Kafka отправляем после коммита (outbox / transactional event).


Пример идеи:

- сохранить stat в таблицу outbox в одной транзакции,

- отдельный воркер читает outbox и отправляет в Kafka.


{{< /details >}}