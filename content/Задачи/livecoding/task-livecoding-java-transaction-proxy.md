#### 46. Собственный прокси для обеспечения отдельной транзакции

**Условие задачи:**  
🔥 Дан класс с методами `methodA()` и `methodB()`, оба помечены аннотацией `@Transactional`, при этом `methodB()` должен выполняться в новой транзакции (`PROPAGATION_REQUIRES_NEW`). Но при вызове `methodB()` изнутри `methodA()` через `this.methodB()` прокси Spring не срабатывает, и новая транзакция не создаётся. Нужно написать свой прокси (или использовать self-injection), чтобы `methodB()` вызывался через прокси.

**Код:**

```java
@Service
public class TransactionService {
    @Transactional
    void methodA() {
        methodB();  // Внутренний вызов – прокси не применяется
    }

    @Transactional(propagation = Propagation.REQUIRES_NEW)
    void methodB() {
        // some logic
    }
}
````

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Spring AOP создаёт прокси вокруг Spring-бина и перехватывает только **внешние** вызовы через прокси.  
💡 Чтобы вызвать `methodB()` через прокси, нужно получить ссылку на сам прокси-бина внутри класса и вызывать `self.methodB()` вместо `this.methodB()`.  
💡 Можно внедрить сам бин `TransactionService` через Spring — он будет прокси-обёрткой.  
{{< /details >}}

{{< details "Решение" close >}}

```java
import org.springframework.aop.framework.AopContext;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Propagation;
import org.springframework.transaction.annotation.Transactional;

@Service
public class TransactionService {

    // Вариант 1: использование AopContext для получения прокси
    @Transactional
    public void methodA() {
        // вместо this.methodB() – получаем текущий прокси
        ((TransactionService) AopContext.currentProxy()).methodB();
    }

    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void methodB() {
        // some logic in a new transaction
    }
}
```

**Дополнительно**, чтобы `AopContext.currentProxy()` работал, нужно включить `exposeProxy`:

```java
@Configuration
@EnableTransactionManagement(proxyTargetClass = true, exposeProxy = true)
public class AppConfig {
    // ...
}
```

---

**Или вариант 2: self-injection прокси-бина через конструктор:**

```java
@Service
public class TransactionService {

    private final TransactionService self;

    public TransactionService(TransactionService self) {
        this.self = self; // это прокси-бина, а не this
    }

    @Transactional
    public void methodA() {
        // вызываем через прокси
        self.methodB();
    }

    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void methodB() {
        // some logic in a new transaction
    }
}
```

📌 **Что важно:**  
✅ В обоих вариантах вызов `methodB()` идёт через Spring-прокси, поэтому аннотация `@Transactional(propagation = REQUIRES_NEW)` будет применена.  
✅ Вариант с `AopContext` требует `exposeProxy = true` в настройках AOP.  
✅ Self-injection конструктором не требует дополнительных настроек, но важно, чтобы Spring корректно внедрил прокси, а не оригинальный объект.  
{{< /details >}}