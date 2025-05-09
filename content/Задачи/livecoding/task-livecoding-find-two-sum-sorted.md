#### 3. Найти 2 элемента упорядоченного массива, сумма которых равна заданному числу

{{< hint warning >}}
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}
- Так как массив **упорядоченный**, можно использовать два указателя: один в начале, другой в конце.
- Если сумма элементов больше целевого числа, уменьшаем правый указатель.
- Если сумма меньше, увеличиваем левый указатель.
- Если сумма совпала — нашли ответ.
- Если указатели пересеклись — таких элементов нет.
- Временная сложность: **O(n)**, так как массив проходит максимум один раз.
{{< /details >}}

{{< details "Решение" close >}}

```java
public class TwoSumSorted {
    public static int[] findTwoSum(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        
        while (left < right) {
            int sum = nums[left] + nums[right];

            if (sum == target) {
                return new int[]{nums[left], nums[right]};
            } else if (sum < target) {
                left++;  // Увеличиваем левый индекс, чтобы увеличить сумму
            } else {
                right--; // Уменьшаем правый индекс, чтобы уменьшить сумму
            }
        }
        
        return new int[]{}; // Если ничего не нашли
    }

    public static void main(String[] args) {
        int[] nums = {1, 2, 3, 4, 5, 6, 7, 8, 9};
        int target = 10;
        int[] result = findTwoSum(nums, target);

        if (result.length > 0) {
            System.out.println("Найденные элементы: " + result[0] + " и " + result[1]);
        } else {
            System.out.println("Пара не найдена");
        }
    }
}
```

**Пример работы:**  
Вход: `{1, 2, 3, 4, 5, 6, 7, 8, 9}`, `target = 10`  
Выход: `1 и 9` (или `2 и 8`, `3 и 7`, `4 и 6` в зависимости от того, какую пару он найдет первой).
{{< /details >}}

