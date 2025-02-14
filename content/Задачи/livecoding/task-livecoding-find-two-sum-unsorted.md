#### 4. Найти два элемента в неупорядоченном массиве, сумма которых равна заданному числу?

{{< hint warning >}}
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}
- Для **неупорядоченного** массива мы не можем использовать бинарный поиск.
- **Оптимальный подход** — использовать `HashMap`, сохраняя разницу `target - num`.
- Можно также использовать `Set`, чтобы проверять, встречался ли уже нужный элемент.
- **Менее эффективный** способ — два вложенных цикла (`O(n^2)`).
{{< /details >}}

{{< details "Решение" close >}}

```java
import java.util.*;

public class TwoSum {
    public static int[] findTwoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>(); // Число -> Индекс
        for (int i = 0; i < nums.length; i++) {
            int complement = target - nums[i]; // Число, которое нужно найти
            if (map.containsKey(complement)) {
                return new int[]{map.get(complement), i}; // Найденные индексы
            }
            map.put(nums[i], i); // Добавляем число в map
        }
        return new int[]{}; // Если пары не найдено
    }

    public static void main(String[] args) {
        int[] nums = {4, 7, 1, -3, 2};
        int target = 5;
        int[] result = findTwoSum(nums, target);
        if (result.length == 2) {
            System.out.println("Индексы: " + result[0] + ", " + result[1]);
        } else {
            System.out.println("Пара не найдена");
        }
    }
}
```

**Разбор кода:**

- Используем `HashMap`, чтобы хранить число и его индекс.
- Для каждого элемента вычисляем `complement = target - num`.
- Если `complement` уже в `map`, значит нашли пару.
- Время выполнения: **`O(n)`** (проходим массив один раз).
{{< /details >}}