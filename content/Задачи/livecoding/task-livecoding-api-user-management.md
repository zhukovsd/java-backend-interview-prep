#### 8. Спроектировать API для управления пользователем и изменения отдельных полей?


{{< hint warning >}}
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}
- Для создания API для управления пользователем необходимо использовать **RESTful** подход.
- Используем стандартные HTTP методы:
    - **`GET`** для получения данных пользователя,
    - **`POST`** для создания нового пользователя,
    - **`PUT`** или **`PATCH`** для обновления данных пользователя.
- Для изменения **отдельных полей** можно использовать **`PATCH`**, так как это подходит для частичных обновлений.
- Также стоит продумать валидацию данных и обработку ошибок.
- Рекомендуется использовать модель **DTO** (Data Transfer Object) для передачи данных.
{{< /details >}}

{{< details "Решение" close >}}
Предположим, что у нас есть таблица пользователей **users**, структура которой следующая:

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100),
    age INT
);
```

API для управления пользователем:

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

    private final UserService userService;

    @Autowired
    public UserController(UserService userService) {
        this.userService = userService;
    }

    // Получение данных пользователя
    @GetMapping("/{id}")
    public ResponseEntity<User> getUser(@PathVariable("id") int id) {
        User user = userService.getUserById(id);
        if (user == null) {
            return ResponseEntity.notFound().build();
        }
        return ResponseEntity.ok(user);
    }

    // Создание нового пользователя
    @PostMapping
    public ResponseEntity<User> createUser(@RequestBody UserDTO userDTO) {
        User user = userService.createUser(userDTO);
        return ResponseEntity.status(HttpStatus.CREATED).body(user);
    }

    // Обновление всех данных пользователя
    @PutMapping("/{id}")
    public ResponseEntity<User> updateUser(@PathVariable("id") int id, @RequestBody UserDTO userDTO) {
        User updatedUser = userService.updateUser(id, userDTO);
        if (updatedUser == null) {
            return ResponseEntity.notFound().build();
        }
        return ResponseEntity.ok(updatedUser);
    }

    // Частичное обновление данных пользователя
    @PatchMapping("/{id}")
    public ResponseEntity<User> updateUserPartial(@PathVariable("id") int id, @RequestBody UserDTO userDTO) {
        User updatedUser = userService.updateUserPartial(id, userDTO);
        if (updatedUser == null) {
            return ResponseEntity.notFound().build();
        }
        return ResponseEntity.ok(updatedUser);
    }
}
```

- **`GET /api/users/{id}`**: Этот метод извлекает полную информацию о пользователе по ID.
- **`POST /api/users`**: Этот метод используется для создания нового пользователя, передавая все необходимые поля (например, имя, email и возраст).
- **`PUT /api/users/{id}`**: Этот метод используется для полного обновления данных пользователя. Он обновляет все поля (имя, email, возраст).
- **`PATCH /api/users/{id}`**: Частичное обновление данных. Мы обновляем только те поля, которые переданы в запросе (например, только возраст или только email).

Пример DTO для передачи данных пользователя:

```java
public class UserDTO {
    private String name;
    private String email;
    private Integer age;

    // getters and setters
}
```

В классе **UserService** можно будет реализовать логику работы с данными пользователя:

```java
@Service
public class UserService {

    // Реализуем методы для работы с пользователем:
    public User getUserById(int id) {
        // Получение пользователя по id из базы данных
    }

    public User createUser(UserDTO userDTO) {
        // Создание нового пользователя
    }

    public User updateUser(int id, UserDTO userDTO) {
        // Обновление всех данных пользователя
    }

    public User updateUserPartial(int id, UserDTO userDTO) {
        // Частичное обновление данных пользователя
    }
}
```

 **Ключевые моменты:**

- Используем стандартные HTTP методы для создания, чтения, обновления и удаления данных.
- **`PATCH`** идеально подходит для частичного обновления полей.
- Важно следить за валидацией данных (например, используя аннотации валидации **@NotNull, @Size**).
- Обработка ошибок (например, возврат **404** при отсутствии пользователя).
{{< /details >}}