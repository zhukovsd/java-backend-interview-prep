#### 24. Поиск первого уникального символа


**Условие задачи:**  
🔍 **Реализовать метод `findFirstUniqueLetter`, который находит первый уникальный символ в строке.**  
📌 **Если все символы повторяются, можно вернуть пробел `' '` или специальный символ.**

📌 **Примеры:**

```java
findFirstUniqueLetter("summer time sadness") -> 'u'
findFirstUniqueLetter("AA bb CC d") -> 'd'
```

**Код с собеседования**

```java
public class Main {
    public static void main(String[] args) {
        test();
    }

    private static char findFirstUniqueLetter(String str) {
        // Write your code here
    }

    private static void test() {
        Map<String, Character> testInOut = new HashMap<>() {{
            put(""summer time sadness"", 'u');
            put(""AA bb CC d"", 'd');
        }};

        testInOut.entrySet().forEach(entry -> {
            char answer = findFirstUniqueLetter(entry.getKey());
            if (answer == entry.getValue()) System.out.println(""Passed"");
            else System.out.printf(""Not passed. Received %s but expected %s\n"", answer, entry.getValue());
        });
    }
}
```



{{< hint warning >}}
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}
💡 Можно использовать `LinkedHashMap`, `HashMap` или `int[]` для подсчета количества вхождений.  
💡 `LinkedHashMap` сохраняет порядок добавления, что поможет найти первый уникальный символ.  
💡 Можно дважды пройти по строке: сначала посчитать частоту, затем найти первый уникальный символ.
{{< /details >}}

{{< details "Решение" close >}}

📌 **1. Решение через `LinkedHashMap` (сохраняет порядок добавления):**

```java
import java.util.LinkedHashMap;
import java.util.Map;

public class Main {
    public static void main(String[] args) {
        test();
    }

    private static char findFirstUniqueLetter(String str) {
        Map<Character, Integer> charCount = new LinkedHashMap<>();

        // Считаем количество вхождений символов
        for (char c : str.toCharArray()) {
            charCount.put(c, charCount.getOrDefault(c, 0) + 1);
        }

        // Находим первый уникальный символ
        for (Map.Entry<Character, Integer> entry : charCount.entrySet()) {
            if (entry.getValue() == 1) {
                return entry.getKey();
            }
        }

        return ' '; // Если нет уникального символа
    }

    private static void test() {
        Map<String, Character> testInOut = new LinkedHashMap<>() {{
            put("summer time sadness", 'u');
            put("AA bb CC d", 'd');
        }};

        testInOut.forEach((input, expected) -> {
            char answer = findFirstUniqueLetter(input);
            if (answer == expected) System.out.println("✅ Passed");
            else System.out.printf("❌ Not passed. Received %s but expected %s\n", answer, expected);
        });
    }
}
```

✅ **Сложность**: O(n) – один проход для подсчета, второй для поиска.

---

📌 **2. Решение через `int[]` (эффективно для английских букв):**

```java
private static char findFirstUniqueLetter(String str) {
    int[] counts = new int[256]; // ASCII символы

    for (char c : str.toCharArray()) {
        counts[c]++;
    }

    for (char c : str.toCharArray()) {
        if (counts[c] == 1) {
            return c;
        }
    }

    return ' ';
}
```

✅ **Этот вариант быстрее, но использует `256` ячеек памяти.**

🚀 **Теперь ты можешь быстро находить уникальные символы! 🔥**
{{< /details >}}