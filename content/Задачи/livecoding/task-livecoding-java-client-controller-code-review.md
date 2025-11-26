#### 95. Code review ClientController и связанных классов

**Условие задачи:**  
📌 Дан REST-контроллер, который должен вернуть полисы клиента. Нужно провести code review:

- найти проблемы (как технические, так и логические),

- предложить, как переписать код более корректно.


**Код:**

```java
@RestController
public class ClientController {

    @Value("policy.limit")
    private int policyLimit;

    @Autowired
    private PolicyService policyService;

    @RequestMapping(path = "client/{clientId}/policies", method = RequestMethod.POST)
    public Response getClientPolicies(@PathVariable("clientId") String clientId) {
        List<PolicyDTO> policies = getPolicies().stream()
            .limit(policyLimit)
            .filter(p -> p.getClientIds().contains(clientId))
            .filter(p -> p.isPaid())
            .toList();
        return new Response(policies);
    }

    @Transactional
    private List<PolicyDTO> getPolicies() {
        return policyService.getPolicies();
    }

    @Data
    @AllArgsConstructor
    public class Response {
        private List<PolicyDTO> policies;
    }
}

@Data
public class PolicyDTO {
    private String id;
    private String name;
    private List<String> clientId;
    private Boolean isPaid;
}

@RequiredArgsConstructor
@Component
public class PolicyService {
    private final PolicyDbRepository repository;
    /**
     *  Читает полисы из БД
     */
    List<PolicyDTO> getPolicies() {
        return repository.getPolicies();
    }
}

public interface PolicyDbRepository {
    List<PolicyDTO> getPolicies();
}
```

---

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 `@Value("policy.limit")` — строка, а не property placeholder. Нужно `@Value("${policy.limit}")`.  
💡 Поля с `@Autowired` → лучше конструкторная инъекция (`@RequiredArgsConstructor`).  
💡 `@RequestMapping POST` на метод, который фактически **читает** данные — по REST лучше `GET`.  
💡 В контроллере вызывается `getPolicies()` с `@Transactional`, но метод `private` + self-invocation → транзакция не сработает.  
💡 `limit(policyLimit)` стоит делать **после** фильтрации, а ещё лучше — на уровне БД.  
💡 `PolicyDTO`: поле `clientId` (в коде вызывается `getClientIds()`) и `Boolean isPaid` (Lombok сгенерирует `getIsPaid()`, а не `isPaid()`) → не скомпилируется.  
💡 Внутренний класс `Response` не `static` → держит ссылку на контроллер, хуже для сериализации/тестов.  
{{< /details >}}

---

{{< details "Решение" close >}}

### 1. Основные проблемы в текущем коде

1. **Неправильное использование `@Value`**

    ```java
    @Value("policy.limit")
    private int policyLimit;
    ```

   Так ты просто внедряешь строку `"policy.limit"` и пытаешься привести к int.  
   ✅ Нужно:

    ```java
    @Value("${policy.limit:100}") // 100 — дефолт
    private int policyLimit;
    ```

2. **Field injection + `@Autowired`**

    - Поля с `@Autowired` хуже тестируются, нельзя сделать `final`, сложно мокать.  
      ✅ Лучше: конструкторная инъекция через `@RequiredArgsConstructor` и `final` поля.

3. **Неверный HTTP-метод**

    ```java
    @RequestMapping(path = "client/{clientId}/policies", method = RequestMethod.POST)
    ```

   Метод фактически читает данные, а не изменяет → по REST должен быть `GET`.  
   ✅

    ```java
    @GetMapping("/client/{clientId}/policies")
    ```

4. **Бесполезная `@Transactional` на `private`-методе**

    ```java
    @Transactional
    private List<PolicyDTO> getPolicies() { ... }
    ```

    - `private`-метод не проксируется Spring'ом, плюс вызов из того же класса → AOP не сработает.

    - Транзакции стоит ставить на **service-слой**, и обычно `readOnly = true` для чтения.

5. **Порядок операций в Stream**

    ```java
    getPolicies().stream()
        .limit(policyLimit)
        .filter(...)
        .filter(...)
    ```

    - Сначала режем до `policyLimit`, а потом фильтруем — в итоге можем получить **меньше** элементов, чем ожидает фронт.  
      ✅ Логичнее: сначала фильтровать, потом `limit`.


    Ещё лучше — вообще не тянуть все полисы в память, а фильтровать на уровне БД.

6. **Проблемы в `PolicyDTO`**

    ```java
    private List<String> clientId;
    private Boolean isPaid;
    ```

    - В контроллере вызывается `p.getClientIds()` и `p.isPaid()`, но таких методов Lombok не сгенерирует.

    - Для `Boolean isPaid` Lombok создаст `getIsPaid()`, а не `isPaid()`.  
      ✅ Лучше:


    ```java
    private List<String> clientIds;
    private boolean paid; // примитив, без null
    ```
    
    Тогда Lombok сгенерирует `getClientIds()` и `isPaid()`.

7. **Внутренний класс Response не static**

    ```java
    @Data
    @AllArgsConstructor
     public class Response { ... }
    ```

   Не-static inner class держит ссылку на внешний `ClientController`.  
   ✅ Сделать `static` или вынести в отдельный класс / `record`.

8. **Фильтрация в контроллере**  
   Контроллер сейчас:

    - вытаскивает все полисы из БД через сервис,

    - фильтрует,

    - режет по лимиту.


    Логика фильтрации — это бизнес-логика, её место в `PolicyService`.


---

### 2. Пример исправленной версии

**Контроллер:**

```java
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.RequiredArgsConstructor;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/client")
@RequiredArgsConstructor
public class ClientController {

    @Value("${policy.limit:100}")
    private int policyLimit;

    private final PolicyService policyService;

    @GetMapping("/{clientId}/policies")
    public Response getClientPolicies(@PathVariable String clientId) {
        List<PolicyDTO> policies = policyService.getClientPolicies(clientId, policyLimit);
        return new Response(policies);
    }

    @Data
    @AllArgsConstructor
    public static class Response {
        private List<PolicyDTO> policies;
    }
}
```

**DTO:**

```java
import lombok.Data;

import java.util.List;

@Data
public class PolicyDTO {
    private String id;
    private String name;
    private List<String> clientIds;  // множественное число
    private boolean paid;            // примитив → isPaid()
}
```

**Сервис:**

```java
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;
import java.util.Objects;

@Service
@RequiredArgsConstructor
public class PolicyService {

    private final PolicyDbRepository repository;

    /**
     * Читает полисы клиента с учётом лимита.
     * Логику фильтрации держим на уровне сервиса.
     */
    @Transactional(readOnly = true)
    public List<PolicyDTO> getClientPolicies(String clientId, int limit) {
        return repository.getPolicies().stream()
                .filter(Objects::nonNull)
                .filter(p -> p.getClientIds() != null && p.getClientIds().contains(clientId))
                .filter(PolicyDTO::isPaid)
                .limit(limit)
                .toList();
    }
}
```

**Репозиторий (как был):**

```java
public interface PolicyDbRepository {
    List<PolicyDTO> getPolicies();
}
```

> На практике лучше сделать методы сразу с фильтрацией в БД, например:  
> `List<PolicyDTO> findByClientIdAndPaidTrue(String clientId, Pageable pageable);`  
> чтобы не тащить все полисы в память.

---

### 3. Кратко, что бы я сказал на собесе

- Исправил DI: конструктор, `final` поля.

- Исправил `@Value` и HTTP-метод (POST → GET).

- Убрал бессмысленную `@Transactional` с private/self-invocation, перенёс транзакцию на сервис.

- Починил DTO (имена полей и типы), чтобы `Stream`-логика вообще компилировалась.

- Перенёс бизнес-логику фильтрации из контроллера в сервис.

- Обратил внимание, что лучше фильтровать на уровне БД, а не в памяти.


{{< /details >}}