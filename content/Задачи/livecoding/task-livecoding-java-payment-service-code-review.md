#### 92. Code review PaymentService: транзакции, DI и логические баги


**Условие задачи:**  
📌 Дан сервис `PaymentService`. Нужно:

- провести **code review**,

- указать проблемы,

- показать, **как исправить код** (структуру оставить примерно ту же).


**Код:**

```java
@Component
public class PaymentService{

    @Autowired
    private ComissionServiceImpl comissionService;

    private NotificationService notificationService = new NotificationServiceImpl();

    @Autowired
    private AccountRepository accountRepository;

    public void makePayment(AccountDto acc1, AccountDto acc2, Integer moneyAmount){
        System.out.println("Start payment");
        var account1 = accountRepository.findById(acc1.getAccountId());
        var account2 = accountRepository.findById(acc1.getAccountId());
        transfer(account1, account2, moneyAmount);
        notificationService.sendNotification(account1.getAccountId());
        notificationService.sendNotification(account2.getAccountId());
        System.out.println("End payment");
    }

    @Transactional
    public void transfer(Account acc1, Account acc2, Integer moneyAmount){
        account1.setMoneyAmount(account1.getMoneyAmount() - moneyAmount);
        account2.setMoneyAmount(account2.getMoneyAmount() + moneyAmount);
    }

}

public class AccountDto {
    private UUID accountId;
    private Integer moneyAmount;
}

@Entity
@Table
public class Account{

    @Id
    private UUID accountId;
    private Integer moneyAmount;
}
```

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Инъекция зависимостей: не используем `new`, лучше **constructor injection** + интерфейсы, а не конкретные классы.  
💡 `findById` в Spring Data возвращает `Optional<Account>`, а не `Account`. Нужна обработка.  
💡 Во втором `findById` используется `acc1`, а должно быть `acc2`.  
💡 В `transfer` параметры называются `acc1/acc2`, но внутри используются `account1/account2` — код не компилируется.  
💡 `@Transactional` не сработает, если аннотированный метод вызывается **из этого же класса** (self-invocation). Транзакцию нужно ставить на entrypoint (`makePayment`) или вынести `transfer` в отдельный сервис.  
💡 Логирование лучше делать через `Logger`, а не `System.out.println`.  
💡 Денежные суммы лучше хранить в `BigDecimal`, но для livecoding можно оставить `Integer` и хотя бы проверить баланс и неотрицательность суммы.  
{{< /details >}}

{{< details "Решение" close >}}

Ниже пример исправленного варианта с комментариями:

```java
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import lombok.RequiredArgsConstructor;

@Service
@RequiredArgsConstructor
public class PaymentService {

    private final CommissionService commissionService;    // интерфейс, а не реализация
    private final NotificationService notificationService;
    private final AccountRepository accountRepository;

    @Transactional
    public void makePayment(AccountDto fromDto, AccountDto toDto, Integer amount) {
        if (amount == null || amount <= 0) {
            throw new IllegalArgumentException("Amount must be positive");
        }

        System.out.println("Start payment"); // в реальном коде: логгер

        // 1. Загружаем счета
        Account from = accountRepository.findById(fromDto.getAccountId())
                .orElseThrow(() -> new IllegalArgumentException("Source account not found"));

        Account to = accountRepository.findById(toDto.getAccountId())
                .orElseThrow(() -> new IllegalArgumentException("Target account not found"));

        // 2. Считаем комиссию (если нужна)
        int commission = commissionService.calculateCommission(amount);
        int totalDebit = amount + commission;

        if (from.getMoneyAmount() < totalDebit) {
            throw new IllegalStateException("Insufficient funds");
        }

        // 3. Переводим деньги (внутри той же транзакции)
        transfer(from, to, amount, commission);

        // 4. Отправляем уведомления
        notificationService.sendNotification(from.getAccountId());
        notificationService.sendNotification(to.getAccountId());

        System.out.println("End payment");
    }

    // package-private / private: вызывать только внутри текущей транзакции
    void transfer(Account from, Account to, int amount, int commission) {
        from.setMoneyAmount(from.getMoneyAmount() - amount - commission);
        to.setMoneyAmount(to.getMoneyAmount() + amount);

        // Если Account — JPA entity, полученная через repository,
        // можно не вызывать save(): изменения зафиксируются через dirty checking.
        // Явный save не повредит, но не обязателен:
        // accountRepository.save(from);
        // accountRepository.save(to);
    }
}
```

Пример интерфейсов (для полноты картины):

```java
public interface CommissionService {
    int calculateCommission(int amount);
}

public interface NotificationService {
    void sendNotification(UUID accountId);
}
```

Пример DTO / Entity (минимально, чтобы стало понятно):

```java
public class AccountDto {
    private UUID accountId;
    private Integer moneyAmount;

    public UUID getAccountId() {
        return accountId;
    }

    public Integer getMoneyAmount() {
        return moneyAmount;
    }

    // setters/конструктор по необходимости
}

@Entity
@Table(name = "accounts")
public class Account {

    @Id
    private UUID accountId;

    private Integer moneyAmount;

    public UUID getAccountId() {
        return accountId;
    }

    public Integer getMoneyAmount() {
        return moneyAmount;
    }

    public void setMoneyAmount(Integer moneyAmount) {
        this.moneyAmount = moneyAmount;
    }
}
```

**Кратко, какие проблемы были и как исправили:**

1. **Неверная загрузка второго аккаунта**

    ```java
    var account2 = accountRepository.findById(acc1.getAccountId());
    ```

   👉 Должно быть `acc2.getAccountId()`.

2. **Игнорирование Optional от `findById`**  
   👉 Теперь используем `orElseThrow(...)`.

3. **Неработающий `@Transactional`**  
   👉 Перенесли аннотацию на `makePayment` — entrypoint бизнес-операции.

4. **Поля с `@Autowired` и `new ...`**  
   👉 Заменено на **constructor injection** через `@RequiredArgsConstructor` + интерфейсы.

5. **Опечатка и привязка к реализации**  
   👉 `ComissionServiceImpl` → `CommissionService`. Привязываемся к интерфейсу.

6. **Проблемы с именами переменных в `transfer`**  
   👉 Используем корректные параметры и не обращаемся к несуществующим `account1/account2`.

7. **Отсутствие валидаций и проверки баланса**  
   👉 Добавлены проверки суммы и достаточности средств.

8. **System.out вместо логгера**  
   👉 Для продакшена стоит заменить на `Logger`, но для livecoding оставили как есть, с замечанием.


Такой вариант уже выглядит адекватным ответом на code review на собеседовании.

{{< /details >}}
