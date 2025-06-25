#### 44. Поиск наименее частого слова в строке


**Условие задачи:**  
🔥 Дана очень длинная строка `text`. Необходимо найти слово, которое встречается в ней **минимальное** количество раз (но хотя бы один раз) и вернуть это слово. Если таких слов несколько, вернуть любое из них.

**Код:**

```java
public class RareWordFinder {
    public static String findLeastFrequentWord(String text) {
        // TODO
    }

    public static void main(String[] args) {
        String text = "apple orange banana apple pear banana apple";
        System.out.println(findLeastFrequentWord(text)); // например, "orange" или "pear"
    }
}
```

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Разбейте строку на слова с помощью `String.split("\\s+")`.  
💡 Используйте `Map<String, Integer>` для подсчёта количества вхождений каждого слова.  
💡 Пройдитесь по записи в `Map` и найдите ключ с минимальным значением счётчика.  
💡 Учтите, что строка может содержать пунктуацию — при необходимости очистите слова от знаков препинания.  
{{< /details >}}

{{< details "Решение" close >}}

```java
import java.util.*;

public class RareWordFinder {
    public static String findLeastFrequentWord(String text) {
        // Разбиение на слова
        String[] words = text.split("\\s+");
        Map<String, Integer> freq = new HashMap<>();
        for (String w : words) {
            String word = w.replaceAll("[^a-zA-Z0-9]", "").toLowerCase();
            if (word.isEmpty()) continue;
            freq.put(word, freq.getOrDefault(word, 0) + 1);
        }
        // Поиск слова с минимальной частотой
        String rare = null;
        int minCount = Integer.MAX_VALUE;
        for (Map.Entry<String, Integer> entry : freq.entrySet()) {
            if (entry.getValue() < minCount) {
                minCount = entry.getValue();
                rare = entry.getKey();
            }
        }
        return rare;
    }

    public static void main(String[] args) {
        String text = "apple orange banana! apple, pear? banana apple";
        System.out.println(findLeastFrequentWord(text)); // orange или pear
    }
}
```

{{< /details >}}