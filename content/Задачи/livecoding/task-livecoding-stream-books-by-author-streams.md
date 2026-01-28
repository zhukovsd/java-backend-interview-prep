#### 10. Получить список наименований книг по каждому автору

**Условие задачи:**  
📌 Есть коллекция `Book`, где:

- `author` — автор

- `names` — список названий книг


Нужно:

1. Получить **мапу** вида  
   `author -> список названий книг`

2. ⭐ _Задача со звёздочкой_:  
   вывести **уникальные** наименования книг для каждого автора.


**Код:**

```java
import java.util.List;
import java.util.Set;

public class Main {

    private record Book(String author, List<String> names) { }

    public static void main(String[] args) {

        Set<Book> books = Set.of(
                new Book("Author1", List.of("Name1")),
                new Book("Author1", List.of("Name2")),
                new Book("Author2", List.of("Name3")),
                new Book("Author2", List.of("Name4", "Name3")),
                new Book("Author3", List.of("Name5"))
        );
    }
}
```

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Нужно сгруппировать данные по `author` → `Collectors.groupingBy`.  
💡 Так как у одного `Book` может быть **несколько названий**, потребуется `flatMap`.  
💡 Для уникальности можно собрать данные в `Set`.  
💡 Тип результата: `Map<String, Set<String>>` — самый логичный вариант.  
{{< /details >}}

{{< details "Решение" close >}}

### ⭐ Вариант с уникальными названиями (рекомендуемый)

```java
import java.util.Map;
import java.util.Set;
import java.util.stream.Collectors;

Map<String, Set<String>> booksByAuthor =
        books.stream()
             .collect(Collectors.groupingBy(
                     Book::author,
                     Collectors.flatMapping(
                             book -> book.names().stream(),
                             Collectors.toSet()
                     )
             ));
```

**Результат:**

```
Author1 -> [Name1, Name2]
Author2 -> [Name3, Name4]
Author3 -> [Name5]
```

---

### Вариант без уникальности (если требуется просто список)

```java
import java.util.Map;
import java.util.List;
import java.util.stream.Collectors;

Map<String, List<String>> booksByAuthor =
        books.stream()
             .collect(Collectors.groupingBy(
                     Book::author,
                     Collectors.flatMapping(
                             book -> book.names().stream(),
                             Collectors.toList()
                     )
             ));
```

---

### 🧠 Краткое объяснение (как на собеседовании)

- Я группирую `Book` по автору.

- Так как названия лежат в `List<String>`, использую `flatMapping`, чтобы «расплющить» вложенные списки.

- Для задачи со звёздочкой собираю в `Set`, чтобы убрать дубликаты (`Name3` у `Author2`).


**Сложность:**

- Время: `O(n)`

- Память: `O(n)`


{{< /details >}}