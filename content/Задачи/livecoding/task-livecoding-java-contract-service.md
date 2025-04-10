#### 33. Ревью и улучшение Spring-сервиса работы с контрактами

**Условие задачи:**  
🔥 Проведите ревью кода сервиса для работы с контрактами. Сервис должен иметь два метода:
- **save** — для сохранения контракта;
- **getPage** — для получения контрактов постранично с использованием кэша, чтобы не обращаться к БД каждый раз.

**Код:**

```java
@Service
public class ContractService {

    final public static String KT = "TOPIC";
    final public static int ONPAGE = 10;

    public ContractRepository repo;
    public KafkaTemplate<String, String> kafka;
    public HashMap<Long, List<Contract>> cache;

    void save(Long a, String b) {
        Contract x = new Contract(a, b);
        repo.save(x);
        kafka.send(KT, "contract was created");
    }

    public List<Contract> getPage(int number) {
        if (!cache.containsKey(number)) {
            List<Contract> cs = repo.findAll();
            List<Contract> r = new ArrayList<>();
            for (int i = 0; i < ONPAGE; i++) {
                Contract c = cs.get(i + number * ONPAGE);
                r.add(c);
            }
            cache.put(number, r);
        }
        return cache.get(number);
    }
}
```



{{< hint warning >}} **Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}} 
💡 **Используй инъекцию зависимостей через конструктор** вместо публичных полей для `repo` и `kafka`.  
💡 **Замени тип кэша** с `HashMap<Long, List<Contract>>` на более подходящий тип, например, `Map<Integer, List<Contract>>` с потокобезопасной реализацией (например, `ConcurrentHashMap`).  
💡 **Обновление кэша:** При сохранении нового контракта стоит сбросить кэш, чтобы последующие запросы получали актуальные данные.  
💡 **Обработка границ:** В методе `getPage` предусмотри случай, когда запрошенная страница выходит за пределы имеющихся данных, чтобы не возникала ошибка `IndexOutOfBoundsException`.  
{{< /details >}}

{{< details "Решение" close >}}

```java
import org.springframework.stereotype.Service;
import org.springframework.kafka.core.KafkaTemplate;
import java.util.List;
import java.util.Map;
import java.util.ArrayList;
import java.util.Collections;
import java.util.concurrent.ConcurrentHashMap;

@Service
public class ContractService {

    private static final String KT = "TOPIC";
    private static final int ONPAGE = 10;

    private final ContractRepository repo;
    private final KafkaTemplate<String, String> kafka;

    // Используем потокобезопасный кэш
    private final Map<Integer, List<Contract>> cache = new ConcurrentHashMap<>();

    // Инъекция зависимостей через конструктор
    public ContractService(ContractRepository repo, KafkaTemplate<String, String> kafka) {
        this.repo = repo;
        this.kafka = kafka;
    }

    // Метод сохранения контракта с последующим сбросом кэша
    public void save(Long id, String data) {
        Contract contract = new Contract(id, data);
        repo.save(contract);
        kafka.send(KT, "contract was created");
        // Очистка кэша, чтобы данные оставались актуальными
        cache.clear();
    }

    // Метод получения контрактов по страницам с обработкой границ
    public List<Contract> getPage(int number) {
        return cache.computeIfAbsent(number, page -> {
            List<Contract> allContracts = repo.findAll();
            int start = page * ONPAGE;
            if (start >= allContracts.size()) {
                // Если запрошенная страница выходит за пределы коллекции, вернем пустой список
                return Collections.emptyList();
            }
            int end = Math.min(start + ONPAGE, allContracts.size());
            return new ArrayList<>(allContracts.subList(start, end));
        });
    }
}
```

📌 **Что улучшилось?**  
✅ **Инъекция зависимостей через конструктор:** Улучшает тестируемость и делает код менее зависимым.  
✅ **Потокобезопасный кэш:** Использование `ConcurrentHashMap` обеспечивает корректную работу в многопоточной среде.  
✅ **Сброс кэша при сохранении:** Гарантирует, что кэш не будет содержать устаревшие данные после добавления нового контракта.  
✅ **Обработка границ страницы:** Метод `getPage` возвращает пустой список, если запрошенная страница выходит за пределы имеющихся контрактов, что предотвращает ошибки.  
✅ **Повышенная читаемость и поддерживаемость кода:** Улучшения делают сервис более надежным и соответствующим современным практикам разработки на Spring. {{< /details >}}
