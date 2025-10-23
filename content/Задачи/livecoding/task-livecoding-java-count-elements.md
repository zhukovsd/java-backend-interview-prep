#### 78. Алгоритм подсчёта вхождений элементов списка

🔥 Нужно реализовать метод, который принимает список `Integer` и возвращает структуру с уникальными элементами и количеством их вхождений.

```java
public class Utils {
    // на вход список целых чисел, необходимо получить структуру: уникальные элементы и их кол-во
    // пример: 1 2 11 2 11 9 11 1 2 3
    //
    // структура:
    // 1  -> 2
    // 2  -> 3
    // 11 -> 3
    // 9  -> 1

    public Map<Integer, Integer> countElementItems(List<Integer> list) {
        // код тут
    }
}
```

---

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Для подсчёта удобно использовать `Map<Integer, Integer>`.  
💡 Можно воспользоваться `HashMap` для скорости, либо `TreeMap` для сортировки ключей.  
💡 В Java 8+ можно применять `Collectors.groupingBy(...)`.  
💡 Если список пустой — вернуть пустую структуру.  
{{< /details >}}

{{< details "Решение" close >}}

Реализация через цикл:

```java
public Map<Integer, Integer> countElementItems(List<Integer> list) {
    Map<Integer, Integer> result = new HashMap<>();
    for (Integer value : list) {
        result.put(value, result.getOrDefault(value, 0) + 1);
    }
    return result;
}
```

Реализация через Stream API:

```java
   public static Map<Integer, Integer> countElementItems(List<Integer> list) {
    return list.stream()
            .collect(Collectors.groupingBy(i -> i, Collectors.summingInt(e -> 1)));
}
```

- В первом варианте `Map<Integer, Integer>`.

- Во втором — `Map<Integer, Long>` (так как `counting()` возвращает `Long`).


---

📌 Итог:

- Уникальные значения — ключи карты.

- Количество вхождений — значения карты.

- Решение работает за **O(n)** по времени и **O(n)** по памяти.


{{< /details >}}
