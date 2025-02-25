#### 11. Разделение списка на партиции


{{< hint warning >}}
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}
🔹 Нам нужно разделить список на **подсписки фиксированного размера**.  
🔹 Если длина списка **не делится нацело**, последний подсписок будет короче.  
🔹 Можно использовать **подход с циклами** или **Stream API**.
{{< /details >}}

{{< details "Решение" close >}}
**1️⃣ Способ: Обычный цикл**  
Простой и понятный способ с использованием `subList()`:

```java
import java.util.ArrayList;
import java.util.List;

class PartitionList {
    public static <T> List<List<T>> partition(List<T> list, int size) {
        List<List<T>> partitions = new ArrayList<>();
        for (int i = 0; i < list.size(); i += size) {
            partitions.add(list.subList(i, Math.min(i + size, list.size())));
        }
        return partitions;
    }

    public static void main(String[] args) {
        List<Integer> numbers = new ArrayList<>();
        for (int i = 1; i <= 10; i++) {
            numbers.add(i);
        }

        List<List<Integer>> result = partition(numbers, 3);
        System.out.println(result);
    }
}
```

✅ **Вывод:** `[[1, 2, 3], [4, 5, 6], [7, 8, 9], [10]]`

**2️⃣ Способ: Stream API (Java 8+)**  
Используем `IntStream` для разделения списка:

```java
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collectors;
import java.util.stream.IntStream;

class PartitionListStream {
    public static <T> List<List<T>> partition(List<T> list, int size) {
        return IntStream.range(0, (int) Math.ceil((double) list.size() / size))
                .mapToObj(i -> list.subList(i * size, Math.min((i + 1) * size, list.size())))
                .collect(Collectors.toList());
    }

    public static void main(String[] args) {
        List<Integer> numbers = new ArrayList<>();
        for (int i = 1; i <= 10; i++) {
            numbers.add(i);
        }

        List<List<Integer>> result = partition(numbers, 3);
        System.out.println(result);
    }
}
```

✅ **Вывод:** `[[1, 2, 3], [4, 5, 6], [7, 8, 9], [10]]`

💡 **Вывод:** Обычный цикл проще, но **Stream API** более декларативный 🚀
{{< /details >}}
