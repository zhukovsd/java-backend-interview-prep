#### 106. Работа с Set: объект Ball, equals / hashCode

**Условие задачи:**  
📌 Нужно:

1. Создать объект **Ball** с полями `size` и `color`.

2. Создать **4 мяча**:

    - 3 разных

    - 1 должен **совпадать** с одним из уже созданных

3. Добавить мячи в `Set`.

4. Показать, что дубликат **не добавился**.


**Код:**

```java
// Создать класс Ball(size, color)
// Создать 4 объекта Ball
// Добавить их в Set
```

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 `Set` использует `equals()` и `hashCode()` для определения уникальности.  
💡 Если их не переопределить — каждый объект будет считаться уникальным.  
💡 Чтобы 2 мяча считались одинаковыми, `equals` и `hashCode` должны учитывать `size` и `color`.  
{{< /details >}}

{{< details "Решение" close >}}

```java
import java.util.HashSet;
import java.util.Objects;
import java.util.Set;

public class Main {

    public static void main(String[] args) {

        Ball ball1 = new Ball(5, "red");
        Ball ball2 = new Ball(6, "blue");
        Ball ball3 = new Ball(7, "green");
        Ball ball4 = new Ball(5, "red"); // совпадает с ball1

        Set<Ball> balls = new HashSet<>();
        balls.add(ball1);
        balls.add(ball2);
        balls.add(ball3);
        balls.add(ball4); // не добавится

        System.out.println("Количество мячей в Set: " + balls.size());
        balls.forEach(System.out::println);
    }
}

class Ball {
    private final int size;
    private final String color;

    public Ball(int size, String color) {
        this.size = size;
        this.color = color;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Ball ball)) return false;
        return size == ball.size &&
               Objects.equals(color, ball.color);
    }

    @Override
    public int hashCode() {
        return Objects.hash(size, color);
    }

    @Override
    public String toString() {
        return "Ball{size=" + size + ", color='" + color + "'}";
    }
}
```

**Что произойдёт:**

- `ball1` и `ball4` равны по `equals()`

- их `hashCode()` совпадает

- `HashSet` добавит только один из них


👉 В итоге в `Set` будет **3 элемента**, а не 4.

{{< /details >}}