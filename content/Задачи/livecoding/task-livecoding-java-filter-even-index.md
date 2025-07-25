#### 63. Удаление нечётных элементов списка

**Условие задачи:**  
📌 Дан список строк `inList`. Нужно получить новый список, в котором **удалены все элементы с нечётными индексами** (то есть оставить элементы с индексами 0, 2, 4, …).

**Код:**

```java
import java.util.*;

public class Main {
    public static void main(String[] args) {
        List<String> inList = List.of(""a"", ""b"", ""c"", ""d"", ""e"", ""g"");

        List<String> outList = inList /*do something*/ ;

        System.out.println(outList); //[a, c, e]
    }
}
````

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Используйте `IntStream.range(0, inList.size())` для генерации индексов.  
💡 Отфильтруйте только чётные индексы: `i % 2 == 0`.  
💡 Преобразуйте индексы обратно в элементы списка через `mapToObj(inList::get)`.  
💡 Соберите результат в `List` через `Collectors.toList()`.  
{{< /details >}}

{{< details "Решение" close >}}

```java
import java.util.*;
import java.util.stream.*;

public class Main {
    public static void main(String[] args) {
        List<String> inList = List.of("a", "b", "c", "d", "e", "g");

        List<String> outList = IntStream.range(0, inList.size())
            .filter(i -> i % 2 == 0)
            .mapToObj(inList::get)
            .collect(Collectors.toList());

        System.out.println(outList); // [a, c, e]
    }
}
```

{{< /details >}}