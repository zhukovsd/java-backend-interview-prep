#### 40. Реализация `hashCode` для класса Point

**Условие задачи:**  
📌 Реализовать метод `hashCode()` для класса `Point` с полями `int x` и `int y`. Объяснить, что возвращает `hashCode()`.

**Код:**

```java
public class Point {
    private int x;
    private int y;

    // конструкторы, геттеры/сеттеры

    @Override
    public int hashCode() {
        // TODO: ваша реализация
    }
}
```


{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Для смешивания полей используйте **простое умножение на простое число** (часто 31).  
💡 Начальная величина результата может быть, например, `17` или `x`.  
💡 Формула:

```java
int result = 17;
result = 31 * result + x;
result = 31 * result + y;
return result;
```

💡 Можно воспользоваться утилитой `Objects.hash(x, y)` для краткости, но лучше понять классическое решение.  
{{< /details >}}

{{< details "Решение" close >}}

```java
import java.util.Objects;

public class Point {
    private int x;
    private int y;

    // Конструктор, геттеры/сеттеры omitted for brevity

    @Override
    public int hashCode() {
        int result = 17;           // начальное произвольное ненулевое значение
        result = 31 * result + x;  // учитываем поле x
        result = 31 * result + y;  // учитываем поле y
        return result;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Point)) return false;
        Point other = (Point) o;
        return this.x == other.x && this.y == other.y;
    }
}
```

{{< /details >}}