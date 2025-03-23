#### 19. Рефакторинг кода PersonService

**Условие задачи:**  
🔥 Проведи **рефакторинг**

**Код:**

```java
public class PersonService { 
    // no usages
    @return
    public void getAdultMaleUsersAverageBMI() {
        double totalIMT = 0.0;
        long countOfPerson = 0;
        double heightInMeters = 0d;
        double imt = 0d;
        List<Person> adultPersons = new ArrayList<>();
        try {
            Connection c = DriverManager.getConnection("jdbc:hsqldb:mem:test", "admin", "qwerty54");
            Statement s = c.createStatement();
            ResultSet rs = s.executeQuery("SELECT * FROM person WHERE sex = 'male' AND age >= 18");
            while (rs.next()) {
                Person p = new Person();
                p.setId(rs.getLong("id"));
                p.setSex(rs.getString("sex"));
                p.setName(rs.getString("name"));
                p.setAge(rs.getLong("age"));
                p.setWeight(rs.getLong("weight"));
                p.setHeight(rs.getLong("height"));
                adultPersons.add(p);
            }
            for (Person p : adultPersons) {
                heightInMeters = p.getHeight() / 100d;
                imt = p.getWeight() / (Double) (heightInMeters * heightInMeters);
                totalIMT += imt;
            }
            countOfPerson = adultPersons.size();
        } catch (Exception e) {
            e.printStackTrace();
        }
        System.out.println("Average imt = " + totalIMT / countOfPerson);
    }
}
```



{{< hint warning >}}
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}
💡 **Избавься от ненужного списка `adultPersons`** – сразу считай BMI во время чтения из `ResultSet`.  
💡 **Используй `try-with-resources`** для автоматического закрытия соединений.  
💡 **Избавься от избыточных переменных** – сразу вычисляй `imt` и обновляй `totalIMT`.  
💡 **Добавь логирование ошибок** вместо `printStackTrace()`.
{{< /details >}}

{{< details "Решение" close >}}

```java
import java.sql.*;

public class PersonService { 

    public void getAdultMaleUsersAverageBMI() {
        String query = "SELECT weight, height FROM person WHERE sex = 'male' AND age >= 18";
        double totalIMT = 0.0;
        long countOfPerson = 0;

        try (Connection c = DriverManager.getConnection("jdbc:hsqldb:mem:test", "admin", "qwerty54");
             Statement s = c.createStatement();
             ResultSet rs = s.executeQuery(query)) {

            while (rs.next()) {
                double heightInMeters = rs.getDouble("height") / 100.0;
                double imt = rs.getDouble("weight") / (heightInMeters * heightInMeters);
                totalIMT += imt;
                countOfPerson++;
            }

            if (countOfPerson > 0) {
                System.out.printf("Average BMI = %.2f%n", totalIMT / countOfPerson);
            } else {
                System.out.println("No adult male users found.");
            }

        } catch (SQLException e) {
            System.err.println("Database error: " + e.getMessage());
        }
    }
}
```

📌 **Что улучшилось?**  
✅ **Меньше кода** – убрали лишний список `adultPersons`.  
✅ **Оптимизирована память** – теперь `Person` объекты не создаются, если не нужны.  
✅ **Используется `try-with-resources`** – ресурсы закрываются автоматически.  
✅ **Добавлена проверка на пустой результат** – если мужчин нет, выводится сообщение.  
✅ **Более чистый и понятный код**.
{{< /details >}}