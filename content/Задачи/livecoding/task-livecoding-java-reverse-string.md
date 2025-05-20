#### 39. Реверс строки

**Условие задачи:**  
📌 Напишите метод, который принимает строку и возвращает её реверс.

**Код:**

```java
public class StringUtils {
    public static String reverse(String input) {
        // TODO
    }
}
````


{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Проверьте вход на `null` и при необходимости выбросьте `IllegalArgumentException`.  
💡 Используйте `StringBuilder(input).reverse().toString()` для простого и быстрого реверса.  
💡 Альтернативно можно обойти строку с конца до начала и собирать символы в новый `StringBuilder`.  
{{< /details >}}

{{< details "Решение" close >}}

```java
public class StringUtils {
    public static String reverse(String input) {
        if (input == null) {
            throw new IllegalArgumentException("Input string must not be null");
        }
        return new StringBuilder(input).reverse().toString();
    }

    public static void main(String[] args) {
        System.out.println(reverse("hello"));  // olleh
        System.out.println(reverse("Java"));   // avaJ
    }
}
```
{{< /details >}}