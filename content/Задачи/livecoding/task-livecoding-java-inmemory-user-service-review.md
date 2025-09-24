#### 81. Code Review: In-memory UserService: код-ревью и правки

🔥 Нужно быстро запустить хранение пользователей в памяти сервиса, чтобы использовать из контроллеров и других сервисов. Разберём проблемы и улучшим код для продового использования под нагрузкой.

```java
@Service
public class UserService {
    private Map<Integer, User> usersCache = new HashMap<>();

    public void addUser(User user) {
        usersCache.put(user.getId(), user);
    }

    public User getUser(int id) {
        return usersCache.get(id);
    }

    public List<User> getAllUsers() {
        return new ArrayList<>(usersCache.values());
    }
}
```

---

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Потокобезопасность: `HashMap` не потокобезопасен — возьми `ConcurrentHashMap`.  
💡 Контракты методов: валидация `user`/`id`, поведение при дубликате (id уже существует).  
💡 Иммутабельность: если `User` изменяемый, лучше возвращать копии/DTO, чтобы не ломать инварианты.  
💡 Возвраты: для чтения — `Optional<User>` вместо `null`, для пустых коллекций — `List.of()`.  
💡 API полнота: часто нужны `create/update/delete`, `exists`, `count`.  
💡 Производительность: возвращай неизменяемую копию списка без лишних аллокаций.  
{{< /details >}}

{{< details "Решение" close >}}

```java
@Service
public class UserService {

    // Потокобезопасное, финализированное хранилище
    private final ConcurrentMap<Integer, User> users = new ConcurrentHashMap<>();

    /**
     * Создаёт пользователя. Бросает исключение, если id уже занят.
     */
    public User create(User user) {
        validateUser(user);
        User prev = users.putIfAbsent(user.getId(), user);
        if (prev != null) {
            throw new IllegalStateException("User with id=" + user.getId() + " already exists");
        }
        return user;
    }

    /**
     * Обновляет или создаёт (upsert) пользователя.
     */
    public User upsert(User user) {
        validateUser(user);
        users.put(user.getId(), user);
        return user;
    }

    /**
     * Возвращает пользователя по id.
     */
    public Optional<User> get(int id) {
        return Optional.ofNullable(users.get(id));
    }

    /**
     * Удаляет пользователя по id. Возвращает true, если удалён.
     */
    public boolean delete(int id) {
        return users.remove(id) != null;
    }

    /**
     * Возвращает неизменяемый снимок списка пользователей.
     * Важно: элементы могут быть изменяемыми, продумай иммутабельность User.
     */
    public List<User> getAll() {
        // Быстрый снимок без лишних копий значений
        return List.copyOf(users.values());
    }

    public boolean exists(int id) {
        return users.containsKey(id);
    }

    public int count() {
        return users.size();
    }

    private static void validateUser(User user) {
        if (user == null) throw new IllegalArgumentException("user must not be null");
        if (user.getId() == null) throw new IllegalArgumentException("user.id must not be null");
    }
}
```

Дополнительно к улучшениям:

- Если `User` изменяемый (мутабельный), рассмотри вариант: хранить **иммутабельные** копии (например, `record UserDto(...)`) и возвращать их наружу, чтобы внешний код не менял состояние кэша «мимо» сервиса.
- Для горячего рестарта/отладки можно добавить методы **экспорта/импорта** (JSON снапшот) или периодический дамп на диск.
- Если id генерируется на стороне сервиса, добавь `AtomicInteger/Long` генератор и `create` без id.
- Если нужно TTL или лимит памяти — поверх `ConcurrentHashMap` использовать Caffeine (`Cache`) с политиками истечения/выталкивания.


{{< /details >}}