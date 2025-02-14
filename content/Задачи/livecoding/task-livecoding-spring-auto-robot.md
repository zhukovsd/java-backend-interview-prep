#### 17. Спринг сервис. Идеи по улучшению кода. Как сделать так, чтобы при добавлении нового робота Spring сразу создавал нового робота и нам не пришлось работать ручками

```java
@Component
public class Robot1 {
    public void startWork() {
        // помыть полы
    }
}

@Component
public class Robot2 {
    public void startWork() {
        // почистить стены
    }
}

@Component
public class Robot3 {
    public void startWork() {
        // играет в футбол
    }
}

@Service
public class RobotProcessing {

    @Autowired
    private Robot1 r1;

    @Autowired
    private Robot2 r2;

    @Autowired
    private Robot3 r3;

    public void startAllRobots() {
        r1.startWork();
        r2.startWork();
        r3.startWork();
    }
}

```


{{< hint warning >}}
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}
🤖 Проблема в том, что **при добавлении нового робота нужно вручную менять код в `RobotProcessing`**.  
⚙️ Можно использовать **список `List<Robot>` или `Map<String, Robot>`**, чтобы Spring автоматически собирал всех бинов-роботов.  
📌 **Интерфейс** поможет сделать код гибким и расширяемым.
{{< /details >}}

{{< details "Решение" close >}}

```java
public interface Robot {
    void startWork();
}

@Component
public class Robot1 implements Robot {
    @Override
    public void startWork() {
        System.out.println("Robot1 моет полы");
    }
}

@Component
public class Robot2 implements Robot {
    @Override
    public void startWork() {
        System.out.println("Robot2 чистит стены");
    }
}

@Component
public class Robot3 implements Robot {
    @Override
    public void startWork() {
        System.out.println("Robot3 играет в футбол");
    }
}

@Service
public class RobotProcessing {

    private final List<Robot> robots;

    @Autowired
    public RobotProcessing(List<Robot> robots) {
        this.robots = robots;
    }

    public void startAllRobots() {
        robots.forEach(Robot::startWork);
    }
}
```

📌 **Объяснение:**

- **Добавили интерфейс `Robot`**, чтобы все роботы его реализовывали.
- **Используем `List<Robot>` в `RobotProcessing`**, и Spring автоматически находит все реализации `Robot`.
- **Теперь можно просто создать новый класс с `@Component`, и Spring сам его добавит в список!**

🔥 **Преимущества:**  
✅ **Гибкость** — не нужно изменять `RobotProcessing` при добавлении нового робота.  
✅ **Инкапсуляция** — `RobotProcessing` не зависит от конкретных классов роботов.  
✅ **Меньше кода и ошибок** 🚀
{{< /details >}}
