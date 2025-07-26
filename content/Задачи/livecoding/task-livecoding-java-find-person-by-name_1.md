#### 58. Поиск человека по имени

**Условие задачи:**  
📌 Дан класс `Person` с полями `name` и `age`. Нужно реализовать метод `findPersonByName(List<Person> persons, String name)`, который возвращает `Optional<Person>` первого человека из списка с заданным именем.

**Код:**

```java
import java.util.List;
import java.util.Optional;

class Person {
    private String name;
    private Integer age;

    public Person(String name, Integer age) {
        this.name = name;
        this.age = age;
    }
    public String getName() { return name; }
    public Integer getAge() { return age; }
}

public class PersonService {
    public Optional<Person> findPersonByName(List<Person> persons, String name) {
        // TODO
        return Optional.empty();
    }
}
````

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Используйте `persons.stream()` для создания потока.  
💡 Примените `filter(p -> name.equals(p.getName()))` для поиска по имени.  
💡 Закончите цепочку вызовом `findFirst()`, чтобы получить `Optional<Person>`.  
{{< /details >}}

{{< details "Решение" close >}}

```java
import java.util.List;
import java.util.Optional;

public class PersonService {
    public Optional<Person> findPersonByName(List<Person> persons, String name) {
        return persons.stream()
                      .filter(p -> name.equals(p.getName()))
                      .findFirst();
    }
}
```

{{< /details >}}