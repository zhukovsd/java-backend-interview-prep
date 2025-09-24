#### 76. Реализация проверки продажи товара по времени и типу с условиями if-else

🔥 Нужно написать метод, который проверяет возможность продажи продукта в зависимости от времени и категории.

```java
// Дано
public class IceCream implements Product {};
public abstract class Alcohol implements Product {
    int getAbv(); // крепость
};

// ТЗ
/*
 * 13:00–14:00 — обед, продажа запрещена
 * 23:00–02:59 — запрет на продажу любого алкоголя
 * 03:00–07:59 — запрет на продажу крепкого алкоголя (>=40)
*/
public boolean checkSale(Product item, Integer hour) {
    // код тут
}
```

---

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Сначала проверь обеденное время.  
💡 В диапазоне 23:00–02:59 блокируй любой алкоголь.  
💡 В диапазоне 03:00–07:59 блокируй крепкий алкоголь (`abv >= 40`).  
💡 Для остальных случаев возвращай `true`.  
{{< /details >}}

{{< details "Решение" close >}}

```java
public boolean checkSale(Product item, Integer hour) {
    if (hour == null || hour < 0 || hour > 23) {
        throw new IllegalArgumentException("Некорректный час: " + hour);
    }

    // Обед — запрет всех продаж
    if (hour == 13) {
        return false;
    }

    // Ночной запрет на любой алкоголь
    if (hour >= 23 || hour < 3) {
        if (item instanceof Alcohol) {
            return false;
        }
    }

    // Утренний запрет на крепкий алкоголь
    if (hour >= 3 && hour < 8) {
        if (item instanceof Alcohol && ((Alcohol) item).getAbv() >= 40) {
            return false;
        }
    }

    return true;
}
```

- Простые условия `if-else`.

- Ясная логика шаг за шагом.

- Проверены все описанные диапазоны времени.


{{< /details >}}
