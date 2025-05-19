#### 37. Фильтрация списка пользователей in-place

**Условие задачи:**  
🔥 Реализовать метод `filterUsers`, который **in-place** отфильтровывает список `users`, оставляя только тех, у кого имя начинается с `namePrefix` и возраст больше 18.

```java
import lombok.Data;
import java.util.ArrayList;

@Data
class User {
    private String name;
    private int age;
}

public class UserUtils {
    public static void filterUsers(ArrayList<User> users, String namePrefix) {
        // ваш код
    }
}

```

{{< hint warning >}}
Спойлеры к решению
{{< /hint >}}

{{< details "Подсказки" close >}}
💡 Для удаления элементов in-place используйте Iterator и его метод remove().  
💡 Можно также воспользоваться методом List.removeIf(...) из Java 8.  
💡 Для проверки префикса используйте String.startsWith(namePrefix).  
💡 Условие фильтрации: user.getAge() > 18.  
{{< /details >}}

{{< details "Решение" close >}}

```java

import lombok.Data;
import java.util.ArrayList;
import java.util.Iterator;

@Data
class User {
    private String name;
    private int age;
}

public class UserUtils {
    public static void filterUsers(ArrayList<User> users, String namePrefix) {
        Iterator<User> iterator = users.iterator();
        while (iterator.hasNext()) {
            User user = iterator.next();
            if (!user.getName().startsWith(namePrefix) || user.getAge() <= 18) {
                iterator.remove();
            }
        }
    }

    // Альтернативный вариант с removeIf:
    // public static void filterUsers(ArrayList<User> users, String namePrefix) {
    //     users.removeIf(user ->
    //         !user.getName().startsWith(namePrefix) || user.getAge() <= 18
    //     );
    // }

    public static void main(String[] args) {
        ArrayList<User> list = new ArrayList<>();
        list.add(new User("Alice", 20));
        list.add(new User("Bob", 17));
        list.add(new User("Alex", 25));
        list.add(new User("Anna", 18));

        filterUsers(list, "A");
        list.forEach(u -> System.out.println(u.getName() + " " + u.getAge()));
        // Вывод:
        // Alice 20
        // Alex 25
    }
}
{{< /details >}}
