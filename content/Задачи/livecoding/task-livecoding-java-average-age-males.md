#### 61. Средний возраст мужчин в списке

**Условие задачи:**  
📌 Дан класс `Person` с полями `name`, `age`, `sex`. В `main` создаётся список `persons` и стрим. Нужно написать код, который найдёт **средний возраст всех мужчин** из списка.

**Код:**

```java
List<Person> persons =
        Arrays.asList(
    new Person(""Alice"", 25, ""female""),
    new Person(""Bob"", 30, ""male""),
    new Person(""Eve"", 35, ""female""),
    new Person(""Jack"", 28, ""male""));
double averageAgeOfMales = persons.stream()
````

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Используйте `filter(...)` для отбора по `sex.equalsIgnoreCase("male")`.  
💡 Примените `mapToInt(Person::getAge)`, чтобы получить `IntStream`.  
💡 Вызов `average()` вернёт `OptionalDouble`, учитывающий отсутствие элементов.  
{{< /details >}}

{{< details "Решение" close >}}

```java
OptionalDouble averageAgeOfMales = persons.stream()
    .filter(p -> p.getSex().equalsIgnoreCase("male"))
    .mapToInt(Person::getAge)
    .average();
```

{{< /details >}}

