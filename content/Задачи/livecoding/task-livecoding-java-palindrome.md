#### 67. Проверка строки на палиндром

```java
package app;

import java.util.Arrays;
import java.util.stream.Collectors;

/**
 * Описание: Палиндром — фраза которая наоборот читается также как и в
 * оригинале. Шалаш — это палиндром. В единственной строке записана фраза или
 * слово. Буквы могут быть только латинские. Фраза может состоять из строчных и
 * прописных латинских букв, цифр, знаков препинания.
 */

public class Task1 {

    public static void main(String[] args) {
        System.out.println(isPalindrome(""A man, a plan, a canal: Panama"")); // True
        System.out.println(isPalindrome(""I love work in IT""));
    }

    public static boolean isPalindrome(String s) {
        
        }
}
```

{{< hint warning >}}
Спойлеры к решению
{{< /hint >}}

{{< details "Подсказки" close >}}
💡 Убери все символы кроме букв и цифр с помощью replaceAll("[^a-zA-Z0-9]", "").   
💡 Приведи строку к нижнему регистру для сравнения (toLowerCase).   
💡 Сравни строку с её реверсом (new StringBuilder(str).reverse().toString()).   
{{< /details >}}

{{< details "Решение" close >}}

```java
public static boolean isPalindrome(String s) {
    if (s == null) return false;

    String cleaned = s.replaceAll("[^a-zA-Z0-9]", "").toLowerCase();
    return cleaned.equals(new StringBuilder(cleaned).reverse().toString());
}

```

{{< /details >}}