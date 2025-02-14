#### 1. Найти первый неповторяющийся элемент в массиве

```java
public static void main(String[] args) {
        int[] nums = {4, 5, 1, 2, 0, 4, 5, 2};

```

{{< hint warning >}}
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}
- Можно использовать `LinkedHashMap`, так как он сохраняет порядок вставки элементов и позволяет эффективно отслеживать количество вхождений.
- Перебираем массив и заносим элементы в `Map`, увеличивая счетчик повторений.
- Затем снова проходим по массиву и находим первый элемент с `count == 1`.
{{< /details >}}

{{< details "Решение" close >}}

```java
import java.util.LinkedHashMap;
import java.util.Map;

public class FirstUniqueElement {
    public static int findFirstUnique(int[] nums) {
        Map<Integer, Integer> countMap = new LinkedHashMap<>();

        for (int num : nums) {
            countMap.put(num, countMap.getOrDefault(num, 0) + 1);
        }

        for (int num : nums) {
            if (countMap.get(num) == 1) {
                return num;
            }
        }

        return -1; // Если уникального элемента нет
    }

    public static void main(String[] args) {
        int[] nums = {4, 5, 1, 2, 0, 4, 5, 2};
        System.out.println(findFirstUnique(nums)); // 1
    }
}
```

Вывод:

```
1
```

Метод работает за `O(n)`, так как мы дважды проходим по массиву. ✅
{{< /details >}}

