#### 21. Генерация ряда Фибоначчи


**Условие задачи:**  
🔢 **Реализуй метод, который принимает число `n` и возвращает ряд Фибоначчи длиной `n`**.

📌 **Пример:**

```java
Input:  7  
Output: [0, 1, 1, 2, 3, 5, 8]
```





{{< hint warning >}}
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}
💡 **Ряд Фибоначчи** – это последовательность чисел, где каждое число равно сумме двух предыдущих:  
`0, 1, 1, 2, 3, 5, 8, 13, ...`  
💡 Для решения можно использовать **итеративный** или **рекурсивный** подход.  
💡 Оптимальный вариант – использовать **динамическое программирование** или **итерацию**.
{{< /details >}}

{{< details "Решение" close >}}

📌 **1. Итеративный способ (быстрее, не требует много памяти)**

```java
import java.util.ArrayList;
import java.util.List;

public class Fibonacci {
    public static List<Integer> generateFibonacci(int n) {
        List<Integer> sequence = new ArrayList<>();
        if (n <= 0) return sequence;

        sequence.add(0);
        if (n == 1) return sequence;

        sequence.add(1);
        for (int i = 2; i < n; i++) {
            sequence.add(sequence.get(i - 1) + sequence.get(i - 2));
        }
        return sequence;
    }

    public static void main(String[] args) {
        System.out.println(generateFibonacci(7));
    }
}
```

💨 **Сложность алгоритма:** O(n)

---

📌 **2. Рекурсивный способ (медленнее, но красиво)**

```java
import java.util.ArrayList;
import java.util.List;

public class FibonacciRecursive {
    public static List<Integer> generateFibonacci(int n) {
        List<Integer> sequence = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            sequence.add(fib(i));
        }
        return sequence;
    }

    private static int fib(int n) {
        if (n <= 1) return n;
        return fib(n - 1) + fib(n - 2);
    }

    public static void main(String[] args) {
        System.out.println(generateFibonacci(7));
    }
}
```

⚠ **Минус:** экспоненциальная сложность O(2ⁿ), медленно для больших `n`.

---

📌 **3. Оптимизированный рекурсивный способ (через мемоизацию)**

```java
import java.util.HashMap;
import java.util.Map;

public class FibonacciMemoization {
    private static final Map<Integer, Integer> memo = new HashMap<>();

    public static int fib(int n) {
        if (n <= 1) return n;
        if (memo.containsKey(n)) return memo.get(n);
        int result = fib(n - 1) + fib(n - 2);
        memo.put(n, result);
        return result;
    }

    public static void main(String[] args) {
        System.out.println(fib(7));
    }
}
```

⚡ **Плюс:** сложность O(n), запоминает вычисленные значения.

---

🚀 **Вывод:**  
✅ Итеративный способ – лучший по производительности.  
✅ Рекурсия – красива, но требует оптимизации (мемоизация).  
✅ Теперь ты умеешь генерировать ряд Фибоначчи разными способами! 🔥
{{< /details >}}