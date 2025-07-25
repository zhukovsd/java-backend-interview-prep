#### 62. Фильтрация заказов через стратегию с лямбдами

**Условие задачи:**  
📌 Дан класс `Order` с полями `price` (BigDecimal), `date` (LocalDate) и `status` (String). Требуется реализовать стратегию фильтрации списка заказов по разным критериям (по цене, по дате, по статусу), используя **лambdas** вместо конкретных классов стратегий. При этом основная логика фильтрации не должна меняться при добавлении новых критериев.

**Код:**

```java
import java.math.BigDecimal;
import java.time.LocalDate;
import java.util.List;
import java.util.function.Predicate;
import java.util.stream.Collectors;

public class Order {
    private final BigDecimal price;
    private final LocalDate date;
    private final String status;

    public Order(BigDecimal price, LocalDate date, String status) {
        this.price = price;
        this.date = date;
        this.status = status;
    }

    public BigDecimal getPrice() { return price; }
    public LocalDate   getDate()  { return date;  }
    public String      getStatus(){ return status;}

    @Override
    public String toString() {
        return "Order{" + price + ", " + date + ", '" + status + "'}";
    }
}

public class OrderService {
    /**
     * Фильтрует список заказов по заданному условию.
     * @param orders список заказов
     * @param filterStrategy лямбда-стратегия фильтрации
     * @return отфильтрованный список
     */
    public static List<Order> filterOrders(List<Order> orders, Predicate<Order> filterStrategy) {
        // TODO: реализовать метод
        return null;
    }
}

public class Main {
    public static void main(String[] args) {
        List<Order> orders = List.of(
            new Order(new BigDecimal("100.00"), LocalDate.of(2023, 1, 10), "NEW"),
            new Order(new BigDecimal("250.50"), LocalDate.of(2023, 2, 5), "SHIPPED"),
            new Order(new BigDecimal("75.75"),  LocalDate.of(2023, 1, 20), "NEW"),
            new Order(new BigDecimal("300.00"), LocalDate.of(2023, 3, 1), "DELIVERED")
        );

        // Применение различных фильтров (TODO)
    }
}
````

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Основная функция принимает `Predicate<Order>` — это и есть стратегия фильтрации.  
💡 Для фильтрации по цене используйте `o -> o.getPrice().compareTo(new BigDecimal("...")) > 0`.  
💡 Для фильтрации по дате — `o -> o.getDate().isAfter(...)`.  
💡 Для фильтрации по статусу — `o -> o.getStatus().equalsIgnoreCase("...")`.  
💡 Новый фильтр можно добавить просто передав другую лямбду в `filterOrders(...)`.  
{{< /details >}}

{{< details "Решение" close >}}

```java
import java.math.BigDecimal;
import java.time.LocalDate;
import java.util.List;
import java.util.function.Predicate;
import java.util.stream.Collectors;

public class OrderService {
    public static List<Order> filterOrders(List<Order> orders, Predicate<Order> filterStrategy) {
        return orders.stream()
                     .filter(filterStrategy)
                     .collect(Collectors.toList());
    }
}

public class Main {
    public static void main(String[] args) {
        List<Order> orders = List.of(
            new Order(new BigDecimal("100.00"), LocalDate.of(2023, 1, 10), "NEW"),
            new Order(new BigDecimal("250.50"), LocalDate.of(2023, 2, 5), "SHIPPED"),
            new Order(new BigDecimal("75.75"),  LocalDate.of(2023, 1, 20), "NEW"),
            new Order(new BigDecimal("300.00"), LocalDate.of(2023, 3, 1), "DELIVERED")
        );

        // Фильтрация по цене > 200
        List<Order> expensive = OrderService.filterOrders(orders,
            o -> o.getPrice().compareTo(new BigDecimal("200.00")) > 0);
        System.out.println("Expensive: " + expensive);

        // Фильтрация по дате после 2023-01-15
        List<Order> recent = OrderService.filterOrders(orders,
            o -> o.getDate().isAfter(LocalDate.of(2023, 1, 15)));
        System.out.println("Recent: " + recent);

        // Фильтрация по статусу NEW
        List<Order> newOrders = OrderService.filterOrders(orders,
            o -> "NEW".equalsIgnoreCase(o.getStatus()));
        System.out.println("New orders: " + newOrders);

        // Добавление нового фильтра без изменения OrderService:
        // заказы, доставленные после 2023-02-01
        List<Order> deliveredAfterFeb = OrderService.filterOrders(orders,
            o -> "DELIVERED".equalsIgnoreCase(o.getStatus())
                 && o.getDate().isAfter(LocalDate.of(2023, 2, 1)));
        System.out.println("Delivered after Feb: " + deliveredAfterFeb);
    }
}
```

{{< /details >}}