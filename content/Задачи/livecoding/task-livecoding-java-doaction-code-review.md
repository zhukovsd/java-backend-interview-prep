#### 102. Code review doAction(): проблемы и рефакторинг

**Условие задачи:**  
📌 Дан метод `doAction(Object source)`, который возвращает строку в зависимости от типа объекта (`Car`, `Train`, `Jet`). Нужно проанализировать проблемы и предложить улучшения/рефакторинг с кодом.

**Код:**

```java
import java.util.List;
import java.util.Set;
import java.util.function.Predicate;
import java.util.function.UnaryOperator;
import java.util.stream.Stream;

public class Main {

    private record Car(String engine) { }
    private record Train(String engine) { }
    private record Jet (String engine) { }

    private record Book(String author, List<String> names) { }

    public static void main(String[] args) {

        // Задание 1: Проанализировать метод doAction(..), предложить возможный рефакторинг, исправления, оптимизацию.
        String doIt = doAction(new Jet("Jet"));
        System.out.println(doIt);
        }

        // Например, в данном случае вызов для Jet будет после проверки всех условий (плохо).
        // Какие могут быть еще проблемы в этом коде ?
        private static String doAction(Object source) {
                String doIt = "Just action ";
                if (source instanceof Car car) {
                        return doIt + car.engine();
                } else if (source instanceof Train train) {
                        return doIt + train.engine();
                } else {
                        return doIt + ((Jet) source).engine();
                }
        }
}
```

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 `else` делает `((Jet) source)` без проверки — если придёт другой тип (или null), будет `ClassCastException` / NPE.  
💡 Метод принимает `Object` — слабый контракт: непонятно, что допустимо.  
💡 Лишние импорты (`Set`, `Predicate`, `UnaryOperator`, `Stream`) и лишний `Book` — шум.  
💡 Замечание про «плохо, что Jet проверяется последним» спорное: обычно это нормально, но проблема масштабируемости реальна (цепочка if/else растёт).  
💡 Лучше использовать sealed interface + pattern matching switch, либо полиморфизм (общий интерфейс).  
{{< /details >}}

{{< details "Решение" close >}}

### Проблемы в текущем doAction()

1. **Опасный каст в else**  
   Если `source` не `Jet`, будет `ClassCastException`:


```java
return doIt + ((Jet) source).engine();
```

2. **Нет обработки null**  
   `source == null` → ветки `instanceof` не сработают → упадём на касте.

3. **Слабый контракт метода**  
   `Object` позволяет передать что угодно (`Book`, `String`, `Integer`) — метод не защищён.

4. **Немасштабируемость**  
   С ростом типов будет расти `if/else`, нарушается OCP.

5. **Код-стайл/шум**  
   Лишние импорты и `Book` не используются → ухудшают читабельность.


---

### Вариант 1 (лучший для современного Java): sealed + switch pattern matching

```java
public class Main {

    sealed interface Vehicle permits Car, Train, Jet {
        String engine();
    }

    record Car(String engine) implements Vehicle {}
    record Train(String engine) implements Vehicle {}
    record Jet(String engine) implements Vehicle {}

    private static String doAction(Vehicle source) {
        String prefix = "Just action ";
        return switch (source) {
            case Car car   -> prefix + car.engine();
            case Train tr  -> prefix + tr.engine();
            case Jet jet   -> prefix + jet.engine();
        };
    }
}
```

✅ Плюсы:

- Нельзя передать «левый» объект — контракт `Vehicle`.

- `switch` исчерпывающий (compiler проверит).

- Нет unsafe cast.

- Легко расширять (добавил тип → компилятор попросит обработать).


---

### Вариант 2: оставить Object, но сделать безопасно (если менять сигнатуру нельзя)

```java
private static String doAction(Object source) {
    if (source == null) {
        throw new IllegalArgumentException("source must not be null");
    }

    String prefix = "Just action ";

    if (source instanceof Car car) {
        return prefix + car.engine();
    }
    if (source instanceof Train train) {
        return prefix + train.engine();
    }
    if (source instanceof Jet jet) {
        return prefix + jet.engine();
    }

    throw new IllegalArgumentException("Unsupported type: " + source.getClass().getName());
}
```

✅ Плюсы:

- Не падаем `ClassCastException` неожиданно.

- Явная ошибка «тип не поддержан».


---

### Мини-рефакторинг по файлу

- Удалить неиспользуемые импорты.

- Удалить `Book`, если не нужен.

- Выровнять скобки в `main` (сейчас есть лишняя `}`).


{{< /details >}}