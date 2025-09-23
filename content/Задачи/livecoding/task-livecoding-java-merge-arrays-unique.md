#### 74. Объединить массивы без дубликатов

🔥 Объедини два списка целых чисел, исключив повторы.

```java
public List<Integer> collect(List<Integer> lst1, List<Integer> lst2) {
    // код тут
}
```

---

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Чтобы убрать дубликаты, используй `Set` (например, `HashSet`).  
💡 Для сохранения порядка можно применить `LinkedHashSet`.  
💡 В Java 8+ удобно стримами: `Stream.concat(...)` и `distinct()`.  
💡 Если важна сортировка, лучше использовать `TreeSet`.  
{{< /details >}}

{{< details "Решение" close >}}

Вариант с `Set`:

```java
public List<Integer> collect(List<Integer> lst1, List<Integer> lst2) {
    Set<Integer> set = new LinkedHashSet<>();
    set.addAll(lst1);
    set.addAll(lst2);
    return new ArrayList<>(set);
}
```

Вариант на Stream API:

```java
public List<Integer> collect(List<Integer> lst1, List<Integer> lst2) {
    return Stream.concat(lst1.stream(), lst2.stream())
                 .distinct()
                 .collect(Collectors.toList());
}
```

- `LinkedHashSet` сохраняет порядок первого появления.

- `distinct()` работает лениво и автоматически убирает дубли.

- Важно: оба решения не зависят от размера входных коллекций.


{{< /details >}}