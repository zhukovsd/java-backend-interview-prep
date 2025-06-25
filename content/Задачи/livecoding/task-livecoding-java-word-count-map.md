#### 45. Подсчет повторений слов в строке

**Условие задачи:**  
🔥 Написать метод, который принимает строку (например, `"тест тест1 нетест слово тест2 тест1 тест тест"`) и возвращает `HashMap<String, Integer>`, где ключ — слово, значение — количество его повторений в строке.

**Код:**

```java
public class WordCounter {
    public static HashMap<String, Integer> countWords(String text) {
        // TODO
    }
}
```

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Разбейте строку на слова с помощью `text.split("\\s+")`.  
💡 Для очистки можно убрать лишние знаки препинания, если они есть (например, `word.replaceAll("\\W+", "")`).  
💡 Используйте `HashMap<String, Integer>` и метод `map.merge(word, 1, Integer::sum)` для подсчёта.  
{{< /details >}}

{{< details "Решение" close >}}

```java
import java.util.HashMap;

public class WordCounter {
    public static HashMap<String, Integer> countWords(String text) {
        HashMap<String, Integer> wordCounts = new HashMap<>();
        if (text == null || text.isEmpty()) {
            return wordCounts;
        }
        String[] words = text.split("\\s+");
        for (String w : words) {
            String word = w.trim();
            if (word.isEmpty()) {
                continue;
            }
            wordCounts.merge(word, 1, Integer::sum);
        }
        return wordCounts;
    }

    public static void main(String[] args) {
        String text = "тест тест1 нетест слово тест2 тест1 тест тест";
        HashMap<String, Integer> counts = countWords(text);
        counts.forEach((word, count) ->
            System.out.println(word + " -> " + count)
        );
        // Ожидаемый вывод:
        // тест -> 3
        // тест1 -> 2
        // нетест -> 1
        // слово -> 1
        // тест2 -> 1
    }
}
```

{{< /details >}}
