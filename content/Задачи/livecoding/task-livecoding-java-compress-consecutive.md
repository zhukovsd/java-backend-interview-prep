#### 54. Сжатие подряд идущих символов по ключу

**Условие задачи:**  
📌 Даны два входных аргумента: строка `text` и целое число `k`. Необходимо заменить в строке все группы подряд идущих одинаковых символов так, чтобы каждые `k` одинаковых символов заменялись одним символом. Например:
- При `k = 1` строка остаётся без изменений.
- При `k = 2` группу `"aa"` заменяем на `"a"`.
- При `k = 3` группу `"aaa"` также на `"a"`.
- Если группа длиннее `k`, например `"cccc"` при `k = 2`, результат — две буквы `"cc"`.

**Код:**

```java
// The main method must be in a class named ""Main"".
class Main {
    public static void main(String[] args) {
        System.out.println(""Hello world!"");
    }
}


Examples:

Input
        aabbababaacccdaaad
K = 2
Output
        abababaccdaad

Input
        aabbababaacccdaaad
K = 1
Output
        aabbababaacccdaaad

Input
        aabbababaacccdaaad
K = 3
Output
aabbababaacdad
````

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Пройдитесь по символам строки один раз, отслеживая текущий символ и длину его группы.  
💡 Когда группа прерывается или строка заканчивается, вычисляйте:

```
groups = length / k;
remainder = length % k;
newLength = groups + remainder;
```

💡 Добавляйте `newLength` копий символа в результат.  
💡 Общая сложность — O(n).  
{{< /details >}}

{{< details "Решение" close >}}

```java
class Main {
    public static void main(String[] args) {
        String input = "aabbababaacccdaaad";
        System.out.println(compress(input, 2)); // abababaccdaad
        System.out.println(compress(input, 1)); // aabbababaacccdaaad
        System.out.println(compress(input, 3)); // aabbababaacdad
    }

    public static String compress(String text, int k) {
        if (k <= 1 || text == null || text.isEmpty()) {
            return text;
        }
        StringBuilder sb = new StringBuilder(text.length());
        char prev = text.charAt(0);
        int count = 1;
        for (int i = 1; i < text.length(); i++) {
            char c = text.charAt(i);
            if (c == prev) {
                count++;
            } else {
                appendCompressed(sb, prev, count, k);
                prev = c;
                count = 1;
            }
        }
        appendCompressed(sb, prev, count, k);
        return sb.toString();
    }

    private static void appendCompressed(StringBuilder sb, char ch, int count, int k) {
        int groups = count / k;
        int remainder = count % k;
        int newLen = groups + remainder;
        for (int i = 0; i < newLen; i++) {
            sb.append(ch);
        }
    }
}
```

{{< /details >}}