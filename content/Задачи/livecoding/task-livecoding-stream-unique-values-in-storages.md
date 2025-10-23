#### 7. Уникальные значения на складах

**Условие задачи:**  
📌 Дана карта складов `Map<String, List<String>> storage`, где ключ — имя склада, а значение — список телефонов/номеров.  
Нужно с помощью **Stream API** вывести все **уникальные значения** (номера), присутствующие на складах.

**Код:**

```java
Map<String, List<String>> storage = new HashMap<>();
storage.put("s1", Arrays.asList("555-1123", "555-3389"));
storage.put("s2", Arrays.asList("555-1123", "555-5264"));
storage.put("s3", Arrays.asList("555-6654", "555-3242"));
```

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Используйте `values()` у `Map`, чтобы получить коллекцию всех списков.  
💡 Примените `flatMap(...)`, чтобы развернуть `List<List<String>>` в `Stream<String>`.  
💡 Метод `distinct()` оставит только уникальные значения.  
💡 Итог можно собрать в `Set` или просто вывести через `forEach(System.out::println)`.  
{{< /details >}}

{{< details "Решение" close >}}

```java
storage.values().stream()            // Stream<List<String>>
    .flatMap(List::stream)           // Stream<String>
    .distinct()                      // убираем дубликаты
    .forEach(System.out::println);   // выводим уникальные значения
```

**Вывод:**

```
555-1123
555-3389
555-5264
555-6654
555-3242
```

🧠 **Пояснение:**

- `flatMap(List::stream)` разворачивает все списки из `Map` в один поток строк.

- `distinct()` автоматически удаляет повторяющиеся элементы.

- Альтернатива — собрать результат в множество:


```java
Set<String> uniqueNumbers = storage.values().stream()
    .flatMap(Collection::stream)
    .collect(Collectors.toSet());
```

{{< /details >}}