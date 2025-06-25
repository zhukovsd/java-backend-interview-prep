#### 51. Потокобезопасный перевод денег между счетами

**Условие задачи:**  
🔥 Есть монолитный сервис `MoneyTransferService` в многопоточной среде. Нужно реализовать метод `transfer(Account from, Account to, BigDecimal amount)`, который гарантирует, что ни один счёт не уйдёт в минус и при этом не возникнет дедлока.

**Код:**

```java
public class MoneyTransferService {
    public void transfer(Account from, Account to, BigDecimal amount) {
        // TODO
    }
}

public class Account {
    private final int id;
    private BigDecimal balance;

    public Account(int id, BigDecimal initial) {
        this.id = id;
        this.balance = initial;
    }

    public int getId() { return id; }
    public BigDecimal getBalance() { return balance; }
    public void debit(BigDecimal amt) { balance = balance.subtract(amt); }
    public void credit(BigDecimal amt) { balance = balance.add(amt); }
}
````

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Для избежания дедлока всегда блокируйте аккаунты в одном и том же порядке (например, по `id`).  
💡 Используйте `synchronized(account)` или `ReentrantLock` на уровне каждого аккаунта для атомарности операций.  
💡 Проверяйте, что на счету `from` достаточно средств перед списанием.  
💡 Обновление балансов обоих аккаунтов должно происходить внутри критической секции.  
{{< /details >}}

{{< details "Решение" close >}}

```java
import java.math.BigDecimal;

public class MoneyTransferService {

    public void transfer(Account from, Account to, BigDecimal amount) {
        // Определяем порядок блокировок по уникальному id, чтобы избежать дедлоков
        Account first = from.getId() < to.getId() ? from : to;
        Account second = from.getId() < to.getId() ? to : from;

        synchronized (first) {
            synchronized (second) {
                // Проверяем, что на счете from достаточно средств
                if (from.getBalance().compareTo(amount) < 0) {
                    throw new IllegalArgumentException(
                        "Недостаточно средств на счете: id=" + from.getId());
                }
                // Выполняем перевод
                from.debit(amount);
                to.credit(amount);
            }
        }
    }
}
```

```java
public class Account {
    private final int id;
    private BigDecimal balance;

    public Account(int id, BigDecimal initial) {
        this.id = id;
        this.balance = initial;
    }

    public int getId() { return id; }
    public BigDecimal getBalance() {
        return balance;
    }

    public void debit(BigDecimal amt) {
        balance = balance.subtract(amt);
    }

    public void credit(BigDecimal amt) {
        balance = balance.add(amt);
    }
}
```

📌 **Что важно помнить:**  
✅ **Порядок блокировок** по `id` предотвращает дедлок в многопоточной среде.  
✅ **Проверка баланса** до списания гарантирует, что счёт не уйдёт в минус.  
✅ **Критическая секция** охватывает обе операции (debit + credit), обеспечивая атомарность и консистентность.  
{{< /details >}}