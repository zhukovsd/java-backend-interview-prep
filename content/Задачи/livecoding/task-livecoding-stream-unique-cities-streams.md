#### 8. Пройти по коллекции и вывести уникальные значения городов

**Условие задачи:**  
📌 Дан список объектов `City`. Нужно:

1. Пройти по коллекции и вывести значения.

2. Создать **уникальную коллекцию строк** (имена городов) через Stream API.


**Код:**

```java
public class Main {
    public static void main(String[] args) {
        List<City> cities = getCity();
        // Код сюда
    }

    private static List<City> getCity() {
        return List.of(
                new City("msk"),
                new City("msk"),
                new City("spb"),
                new City("ekb")
        );
    }

    public class City {
        private String name;

        public City(String name) {
            this.name = name;
        }

        public String getName() {
            return name;
        }
    }
}
```

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Для вывода — простой `forEach`.  
💡 Для уникальных значений:  
 • нужно извлечь `city.getName()`,  
 • вызвать `distinct()`,  
 • собрать в `Set` или `List`.  
💡 Чтобы сохранить порядок — используйте `Collectors.toCollection(LinkedHashSet::new)`.  
{{< /details >}}

{{< details "Решение" close >}}

```java
public class Main {
    public static void main(String[] args) {
        List<City> cities = getCity();

        // 1. Пройти по коллекции и вывести значения
        cities.stream()
                .map(City::getName)
                .forEach(System.out::println);

        // 2. Уникальная коллекция строк (через Stream API)
        Set<String> uniqueNames = cities.stream()
                .map(City::getName)              // достаем строковые значения
                .distinct()                      // убираем дубликаты
                .collect(Collectors.toCollection(LinkedHashSet::new));  // сохраняем порядок

        System.out.println("Уникальные города:");
        uniqueNames.forEach(System.out::println);
    }

    private static List<City> getCity() {
        return List.of(
                new City("msk"),
                new City("msk"),
                new City("spb"),
                new City("ekb")
        );
    }

    public static class City {
        private final String name;
        public City(String name) { this.name = name; }
        public String getName() { return name; }
    }
}
```

**Результат вывода:**

```
msk
msk
spb
ekb
Уникальные города:
msk
spb
ekb
```

**Почему `LinkedHashSet`?**

- `Set` обеспечивает уникальность.

- `LinkedHashSet` дополнительно сохраняет порядок появления элементов — удобно для UI/логов.


{{< /details >}}