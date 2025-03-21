#### 23. Код-ревью и исправление ошибок

**Условие задачи:**  
🔍 Провести **код-ревью** класса `Test`, исправить ошибки и предложить улучшения.


**Код**

```java
public class Test {
    private List<String> list = new LinkedList<String>();

    public synchronized void addString(String s) {
        list.add(s);
    }

    public void addStringsFromFile(String pathToFile) throws IOException {
        File file = new File(pathToFile);
        FileInputStream fis = new FileInputStream(file);
        InputStreamReader isr = new InputStreamReader(fis, StandardCharsets.UTF_8);
        Scanner scanner = new Scanner(isr);
        String str;
        while (scanner.hasNextLine()) {
            str = scanner.nextLine();
            if (!str.isBlank()) {
                addString(str);
            }
        }
    }

    public void removeString(int i) {
        list.remove(i);
    }

    public List<String> getList() {
        return list;
    }

    public synchronized String formatString() {
        String s = """";
        for (int i = 0; i < list.size(); i++) {
            s = s + list.get(i);
        }
        return s;
    }
}

```

{{< hint warning >}}
**Спойлеры к решению**  
{{< /hint >}}


{{< details "Решение" close >}}

**Замеченные проблемы:**  
❌ **Не хватает потокобезопасности.** `synchronized` используется в `addString()` и `formatString()`, но `removeString()` и `getList()` не защищены.  
❌ **Не закрывается `Scanner`.** Это может привести к утечке ресурсов.  
❌ **`FileInputStream` можно заменить на `Files.newBufferedReader()`.** Удобнее и безопаснее.  
❌ **Строки конкатенируются через `+` в `formatString()`.** Это неэффективно, лучше `StringBuilder`.  
❌ **Используется `LinkedList<String>`.** Для частого чтения больше подходит `ArrayList`.

---

**Исправленный код:**

```java
import java.io.*;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Path;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.Scanner;

public class Test {
    private final List<String> list = Collections.synchronizedList(new ArrayList<>());

    public void addString(String s) {
        list.add(s);
    }

    public void addStringsFromFile(String pathToFile) throws IOException {
        try (BufferedReader reader = Files.newBufferedReader(Path.of(pathToFile), StandardCharsets.UTF_8)) {
            String line;
            while ((line = reader.readLine()) != null) {
                if (!line.isBlank()) {
                    addString(line);
                }
            }
        }
    }

    public void removeString(int i) {
        if (i >= 0 && i < list.size()) {
            list.remove(i);
        }
    }

    public List<String> getList() {
        return Collections.unmodifiableList(list);
    }

    public String formatString() {
        synchronized (list) {
            StringBuilder sb = new StringBuilder();
            for (String s : list) {
                sb.append(s);
            }
            return sb.toString();
        }
    }
}
```

---

**Исправления и улучшения:**  
✅ `list` теперь потокобезопасен (`Collections.synchronizedList`).  
✅ `Scanner` заменён на `BufferedReader` + `Files.newBufferedReader()`.  
✅ `formatString()` теперь использует `StringBuilder` (быстрее, чем `s + list.get(i)`).  
✅ `removeString()` теперь проверяет границы индекса.  
✅ `getList()` теперь возвращает **неизменяемый список** (`unmodifiableList()`).

🚀 **Теперь код быстрее, безопаснее и читабельнее!** 🔥

{{< /details >}}