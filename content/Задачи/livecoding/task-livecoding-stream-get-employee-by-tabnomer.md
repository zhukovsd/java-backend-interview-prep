#### 3. Поиск сотрудника по табельному номеру

**Условие задачи:**  
📌 Дан класс `Empl` с полями `tabNomer` и `fio`.  
Необходимо реализовать метод `getEmployee`, который принимает список сотрудников `List<Empl>` и табельный номер `tabNomer`, и возвращает **первого** сотрудника с таким табельным номером в виде `Optional<Empl>`.

**Код:**

```java
public class Empl {
    private String tabNomer;
    private String fio;

    public Empl(String tabNomer, String fio) {
        this.tabNomer = tabNomer;
        this.fio = fio;
    }

    public String getTabNomer() { return tabNomer; }
    public String getFio()     { return fio;     }
}

public class EmplService {
    public Optional<Empl> getEmployee(List<Empl> empls, String tabNomer) {
        // TODO
    }
}
````

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Используйте **Stream API** для элегантного перебора списка.  
💡 Метод `filter(...)` позволит оставить только тех сотрудников, у которых `getTabNomer().equals(tabNomer)`.  
💡 Метод `findFirst()` вернёт первый подходящий элемент в `Optional`.  
💡 Не забывайте проверять входящие параметры на `null`, если нужно повысить надёжность.  
{{< /details >}}

{{< details "Решение" close >}}

```java
import java.util.List;
import java.util.Optional;

public class EmplService {
    public Optional<Empl> getEmployee(List<Empl> empls, String tabNomer) {
        if (empls == null || tabNomer == null) {
            return Optional.empty();
        }
        return empls.stream()
                    .filter(e -> tabNomer.equals(e.getTabNomer()))
                    .findFirst();
    }
}
```

📌 **Что важно помнить:**  
✅ `Optional` — хорошая практика для обозначения возможного отсутствия значения.  
✅ Использование `equals` на константе (`tabNomer.equals(...)`) защищает от NPE, если поле `tabNomer` у сотрудника `null`.  
✅ Stream-подход делает код компактным и читаемым.  
{{< /details >}}