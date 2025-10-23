#### 82. Младший пользователь для каждого имени

**Условие задачи:**  
📌 Дан список пользователей `List<User>`. Нужно вывести **младшего пользователя для каждого имени**.  
Поля класса `User`: `String name`, `String surname`, `int age`.

**Код:**

```java
List<User> users = new ArrayList<>();

users.add(new User("Alex", "Ivanov", 28));
users.add(new User("Alex", "Petrov", 21));

users.add(new User("Mary", "Ivanova", 34));
users.add(new User("Mary", "Petrova", 25));
users.add(new User("Mary", "Sidonova", 33));

users.add(new User("Anton", "Ivanov", 33));
users.add(new User("Anton", "Petrov", 33));
users.add(new User("Anton", "Sidorov", 33));

System.out.println("Hello, World!");
```

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Используйте `Collectors.groupingBy(User::getName)` для группировки по имени.  
💡 Для поиска младшего можно применить `min(Comparator.comparingInt(User::getAge))`.  
💡 После группировки выведите результаты в консоль.  
{{< /details >}}

{{< details "Решение" close >}}

```java
Map<String, Optional<User>> youngestByName = users.stream()
    .collect(Collectors.groupingBy(
        User::getName,
        Collectors.minBy(Comparator.comparingInt(User::getAge))
    ));

youngestByName.forEach((name, userOpt) -> 
    userOpt.ifPresent(user -> 
        System.out.println(name + " -> " + user.getSurname() + ", age " + user.getAge())
    )
);
```

{{< /details >}}