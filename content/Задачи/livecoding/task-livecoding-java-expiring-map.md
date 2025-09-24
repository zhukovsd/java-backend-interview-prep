#### 69. Реализация класса с истекающими ключами

🔥 Написать класс, который позволяет сохранять пары ключ-значение, где каждому ключу ассоциировано время действия. После истечения этого интервала ключ должен стать недоступным.

**Методы класса:**

1. `set(String key, String value, int duration)`

    - Принимает ключ в виде строки, значение типа `String` и длительность в миллисекундах.

    - Ключ становится недоступным после истечения указанной длительности.

    - Метод возвращает `true`, если такой же ключ, который ещё не истек, уже существует, и `false` в противном случае.

    - Значение и длительность должны быть перезаписаны, если ключ уже существует.

2. `get(String key)`

    - Если существует неистекший ключ, метод возвращает связанное с ним значение.

    - В противном случае метод возвращает `-1`.

3. `count()`

    - Возвращает количество ключей, срок действия которых не истек.


---

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Удобно хранить не только значение, но и время истечения — лучше в отдельном классе.  
💡 Для потокобезопасности стоит использовать `ConcurrentHashMap`.  
💡 Проверку истечения (`isExpired`) нужно делать при каждом `get` и `count`.  
💡 При `count()` можно чистить карту от устаревших ключей.  
{{< /details >}}

{{< details "Решение" close >}}

```java
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;

public class ExpiringMap {
    private static class ValueWithExpiry {
        String value;
        long expiryTime;

        ValueWithExpiry(String value, long duration) {
            this.value = value;
            this.expiryTime = System.currentTimeMillis() + duration;
        }

        boolean isExpired() {
            return System.currentTimeMillis() > expiryTime;
        }
    }

    private final Map<String, ValueWithExpiry> storage = new ConcurrentHashMap<>();

    public boolean set(String key, String value, int duration) {
        ValueWithExpiry existing = storage.get(key);
        boolean existedAndNotExpired = existing != null && !existing.isExpired();
        storage.put(key, new ValueWithExpiry(value, duration));
        return existedAndNotExpired;
    }

    public String get(String key) {
        ValueWithExpiry v = storage.get(key);
        if (v == null || v.isExpired()) {
            storage.remove(key);
            return "-1";
        }
        return v.value;
    }

    public int count() {
        storage.entrySet().removeIf(entry -> entry.getValue().isExpired());
        return storage.size();
    }
}
```

{{< /details >}}