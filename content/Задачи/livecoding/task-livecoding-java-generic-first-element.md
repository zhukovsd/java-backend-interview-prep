#### 87. Дженерик: получить первый элемент коллекции произвольного типа

**Условие задачи:**  
📌 Нужно написать метод, который достаёт **один элемент** из коллекции и сохраняет его **тип**. Элементы коллекции могут быть произвольного типа. Желательно, чтобы решение было безопасным по типам и не требовало кастов.

**Код:**

```java
class A {

    public static T getFirstElementOfList(List<T> list){
        return list.size() > 0 ? list.get(0) : null;
    }
}

Object - Number - Integer - AtomicInteger

method(List<> producer)

method(List<> consumer)
```

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 В объявлении метода нужен собственный параметр типа: `public static <T> ...`.  
💡 Возвращать `null` неудобно и небезопасно — лучше `Optional<T>`.  
💡 Для чтения из коллекции используйте wildcard `? extends T` (**PECS**: _Producer Extends_).  
💡 Для записи в коллекцию используйте `? super T` (**Consumer Super**).  
{{< /details >}}

{{< details "Решение" close >}}

```java
import java.util.*;

class A {

    // Безопасный по типам вариант: не возвращаем null
    public static <T> Optional<T> firstOf(List<? extends T> list) {
        if (list == null || list.isEmpty()) return Optional.empty();
        return Optional.ofNullable(list.get(0));
    }

    // Если строго нужен T (не Optional) — допускаем null,
    // но такой вариант менее предпочтителен
    public static <T> T firstOrNull(List<? extends T> list) {
        return (list == null || list.isEmpty()) ? null : list.get(0);
    }

    // Демонстрация PECS для producer/consumer:

    // producer: читаем элементы (коллекция производит T) → ? extends T
    public static <T> void methodProducer(List<? extends T> producer) {
        // можно читать как T
        for (T t : producer) {
            // use t
        }
        // producer.add(...) — НЕЛЬЗЯ (неизвестный подтип)
    }

    // consumer: записываем элементы (коллекция потребляет T) → ? super T
    public static <T> void methodConsumer(List<? super T> consumer, T value) {
        consumer.add(value); // можно добавлять T
        // чтение даёт Object (безопасного downcast нет)
        Object any = consumer.get(0);
    }

    // Примеры использования с иерархией типов:
    public static void main(String[] args) {
        List<Integer> ints = List.of(1, 2, 3);
        List<Number> nums = new ArrayList<Number>(List.of(10.5, 20.0));
        List<Object> objs = new ArrayList<>();

        // firstOf сохраняет точный тип вывода:
        Optional<Integer> i1 = firstOf(ints);      // Optional<Integer>
        Optional<Number>  n1 = firstOf(nums);      // Optional<Number>

        System.out.println(i1.orElse(-1)); // 1
        System.out.println(n1.orElse(-1)); // 10.5

        // PECS:
        methodProducer(ints);              // List<? extends T> — ок для чтения
        methodConsumer(objs, "hello");     // List<? super T> — ок для записи String
        methodConsumer(nums, 42);          // запись Integer в List<? super Integer>
    }
}
```

{{< /details >}}