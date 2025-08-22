#### 65. Исправление класса Cat4

**Условие задачи:**  
🔥 В классе `Cat4` есть ошибки в инициализации полей, работе с потоками и JDBC. Необходимо исправить класс, чтобы он был корректным, потокобезопасным и компилируемым.

```java
public class Cat4 {
    private final ConcurrentHashMap<byte[], BigDecimal> CACHE = new ConcurrentHashMap<>();
    public int jumpsCount = 0;
    private var cat4Profile;
    private final List<Integer> list;
    private final DataSource dataSource;

    public Cat4(DataSource dataSource, List<? extends Integer> list) {
        dataSource = this.dataSource;
        list = this.list;
    }

    public void doRandomJump(int maxJumps) {
        Random rnd = new Random();
        int jumpsToDo = Math.abs(rnd.nextInt()) % maxJumps;
        for (int i = 0; i < jumpsToDo; i++) {
            new Thread(() -> {
                doJump();
            }).start();
        }
    }

    public void setCat4Profile(Cat4Profile cat4Profile) {
        this.cat4Profile = cat4Profile;
    }

    public String getCat4Name() {
        try {
            return this.cat4Profile.getCatName();
        } catch (NullPointerException e) {
            return ""Max"";
        }
    }

    public void doJump() {
        this.jumpsCount++;
        Logger.getLogger(Cat4.class.getName()).fine(""Jump!"");
    }

    public void doMeow() {
        Logger.getLogger(Cat4.class.getName()).fine(""Meow!"");
    }

    public BigDecimal doQuery(byte[] parameters) throws SQLException {
        Connection conn = null;
        Statement stmt = null;
        try {
            conn = dataSource.getConnection();
            stmt = conn.createStatement();
            ResultSet resultSet = stmt.executeQuery(""select weight from Cat where name = '"" + new String(parameters) + ""')"");
            resultSet.next();
            return resultSet.getBigDecimal(""weight"");
        } finally {
            if (stmt != null) {
                stmt.close();
            }
            if (conn != null) {
                conn.close();
            }
        }
    }

    public int getJumpsCount() {
        int result = jumpsCount;
        jumpsCount = 0;
        return result;
    }

    public void setJumpsCount() {
        this.jumpsCount++;
    }
}

```

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Инициализация полей в конструкторе должна быть через `this.field = parameter`.  
💡 Для полей класса нельзя использовать `var`, нужно указать явный тип.  
💡 Увеличение `jumpsCount` в многопоточном режиме должно быть потокобезопасным (`synchronized`).  
💡 Для JDBC лучше использовать try-with-resources.  
💡 Строковые литералы нужно оформлять правильно (`"Max"`, `"Jump!"`, `"Meow!"`).  
{{< /details >}}

{{< details "Решение" close >}}

```java
import java.math.BigDecimal;
import java.sql.*;
import java.util.List;
import java.util.Random;
import java.util.concurrent.ConcurrentHashMap;
import java.util.logging.Logger;
import javax.sql.DataSource;

public class Cat4 {
    private final ConcurrentHashMap<byte[], BigDecimal> CACHE = new ConcurrentHashMap<>();
    public int jumpsCount = 0;
    private Cat4Profile cat4Profile;
    private final List<Integer> list;
    private final DataSource dataSource;

    public Cat4(DataSource dataSource, List<Integer> list) {
        this.dataSource = dataSource;
        this.list = list;
    }

    public void doRandomJump(int maxJumps) {
        Random rnd = new Random();
        int jumpsToDo = Math.abs(rnd.nextInt()) % maxJumps;
        for (int i = 0; i < jumpsToDo; i++) {
            new Thread(this::doJump).start();
        }
    }

    public void setCat4Profile(Cat4Profile cat4Profile) {
        this.cat4Profile = cat4Profile;
    }

    public String getCat4Name() {
        if (cat4Profile != null) {
            return cat4Profile.getCatName();
        }
        return "Max";
    }

    public synchronized void doJump() {
        this.jumpsCount++;
        Logger.getLogger(Cat4.class.getName()).fine("Jump!");
    }

    public void doMeow() {
        Logger.getLogger(Cat4.class.getName()).fine("Meow!");
    }

    public BigDecimal doQuery(byte[] parameters) throws SQLException {
        try (Connection conn = dataSource.getConnection();
             Statement stmt = conn.createStatement();
             ResultSet rs = stmt.executeQuery(
                     "select weight from Cat where name = '" + new String(parameters) + "'")) {
            rs.next();
            return rs.getBigDecimal("weight");
        }
    }

    public synchronized int getJumpsCount() {
        int result = jumpsCount;
        jumpsCount = 0;
        return result;
    }

    public synchronized void incrementJumpsCount() {
        this.jumpsCount++;
    }
}
```

{{< /details >}}