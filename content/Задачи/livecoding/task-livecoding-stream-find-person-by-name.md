#### 9. Поиск человека по имени (Stream API)

**Условие задачи:**  
📌 Дан класс `Person` с полями `name` и `age`.  
Нужно реализовать метод, который **ищет человека по имени** и возвращает `Optional<Person>`.  
Решение удобнее сделать через **Stream API**.

**Код:**

```java
class Person {
    String name;
    Integer age;
}

Optional<Person> findPersonByName(List<Person> persons, String name) {
    // ...
}
```

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Используй `stream()` + `filter(...)`.  
💡 Для получения одного элемента подойдёт `findFirst()`.  
💡 Лучше сравнивать строки через `Objects.equals(...)`, чтобы избежать `NullPointerException`.  
{{< /details >}}

{{< details "Решение" close >}}

```java
import java.util.List;
import java.util.Objects;
import java.util.Optional;

Optional<Person> findPersonByName(List<Person> persons, String name) {
    return persons.stream()
            .filter(p -> Objects.equals(p.name, name))
            .findFirst();
}
```

{{< /details >}}
