#### 28. Исправление ошибок Spring Boot приложения

**Условие задачи:**  
🔥 Исправить ошибки в коде Spring Boot.

**Код:**

```java
package com.home.app;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class App {
    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }
}

package com.home.controller;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@Controller
public class Controller {
    @GetMapping(""/"")
    public String test() {
        return ""test"";
    }
}
```

{{< hint warning >}} 
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}
💡 **Раздели классы по файлам:** Каждый файл должен содержать одно объявление package.  
💡 **Правильные импорты:** Используй `org.springframework.stereotype.Controller` для `@Controller` и `org.springframework.web.bind.annotation.GetMapping` для `@GetMapping`.  
💡 **Синтаксис строк:** Исправь строковый литерал в `@GetMapping("/")` – убедись, что кавычки используются корректно.  
💡 **Проверь структуру проекта:** Файлы должны находиться в директориях, соответствующих их package. {{< /details >}}

{{< details "Решение" close >}}

_Создаем два отдельных файла:_

**1. Файл App.java в пакете com.home.app:**

```java
package com.home.app;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class App {
    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }
}
```

**2. Файл Controller.java в пакете com.home.controller:**

```java
package com.home.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class Controller {
    @GetMapping("/")
    public String test() {
        return "test";
    }
}
```

📌 **Что улучшилось?**  
✅ **Структурирование кода:** Каждый класс находится в отдельном файле с корректной организацией пакетов.  
✅ **Использование правильных импортов:** Аннотации `@Controller` и `@GetMapping` импортированы из правильных пакетов.  
✅ **Правильный синтаксис строк:** Строковый литерал в аннотации `@GetMapping` оформлен корректно.  
✅ **Соответствие стандартам Spring Boot приложения.** 
{{< /details >}}