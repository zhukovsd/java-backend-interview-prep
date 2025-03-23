#### 22. Поиск Person по имени

**Условие задачи:**  
🔍 **Реализовать метод `findPersonByName`, который ищет объект `Person` в списке по имени.**  
📌 **Если человек найден – вернуть `Optional<Person>`, иначе вернуть `Optional.empty()`.**

📌 **Пример:**

```java

class Person {
        String name;
        Integer age;
}

Optional <Person> findPersonByName(List<Person> persons, String name) {
        return null;
}
```


{{< hint warning >}}
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}
**Подсказки:**  
💡 Используй `Optional` для обработки случая, когда человек не найден.  
💡 Можно использовать `stream().filter().findFirst()`, `for-each` или `for`-цикл.  
💡 `equalsIgnoreCase()` поможет искать без учета регистра.
{{< /details >}}

{{< details "Решение" close >}}

**Решение:**

📌 **1. Через Stream API (кратко и элегантно)**

```java
import java.util.List;
import java.util.Optional;

class Person {
    String name;
    Integer age;

    public Person(String name, Integer age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{name='" + name + "', age=" + age + "}";
    }
}

public class PersonFinder {
    public static Optional<Person> findPersonByName(List<Person> persons, String name) {
        return persons.stream()
                .filter(p -> p.name.equalsIgnoreCase(name))
                .findFirst();
    }

    public static void main(String[] args) {
        List<Person> persons = List.of(
            new Person("Alice", 25),
            new Person("Bob", 30)
        );

        System.out.println(findPersonByName(persons, "Alice")); // Optional[Person{name='Alice', age=25}]
        System.out.println(findPersonByName(persons, "John"));  // Optional.empty
    }
}
```

✅ **Сложность**: O(n) – в худшем случае перебираем весь список.

---

📌 **2. Через обычный `for`-цикл**

```java
public static Optional<Person> findPersonByName(List<Person> persons, String name) {
    for (Person person : persons) {
        if (person.name.equalsIgnoreCase(name)) {
            return Optional.of(person);
        }
    }
    return Optional.empty();
}
```

📌 **Когда использовать?** Если не хочется использовать `Stream API`.

---

🚀 **Вывод:**  
✅ **Stream API** – лаконичное и удобное решение.  
✅ **Цикл `for`** – классика, иногда читается проще.  
✅ Теперь ты можешь быстро искать людей по имени! 🔥
{{< /details >}}