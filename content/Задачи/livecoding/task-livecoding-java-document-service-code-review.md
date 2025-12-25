#### 98. Code review: DocumentService и DocumentReader

**Условие задачи:**  
📌 Дан сервис `DocumentService`, который читает документы разных типов с помощью самописного `DocumentReader`.  
Нужно провести **code review**:

- что в коде **ок**,

- что **не ок**,

- что и **как улучшить**.


**Код:**

```java
@Component
public class DocumentService {

    public InputStream readDocument(String type) {
        DocumentReader reader = new DocumentReader();

        if (type.equals("PDF")) {
            return reader.readPdf();
        } else if (type.equals("DOCX")) {
            return reader.readDocx();
        } else if (type.equals("XLSX")) {
            return reader.readXlsx();
        } else {
            return null;
        }
    }
}
```

---

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

---

{{< details "Подсказки" close >}}  
💡 `new DocumentReader()` внутри сервиса — нарушение DI, усложняет тестирование.  
💡 `type.equals("PDF")` может привести к `NullPointerException`.  
💡 `else return null` — плохой контракт метода.  
💡 Условная логика плохо масштабируется (нарушение OCP).  
💡 Лучше использовать `enum`, `Map` или стратегию.  
{{< /details >}}

---

{{< details "Решение" close >}}

### Что в коде **ок** ✅

1. **Простота и читаемость**

    - Логика легко читается: по типу документа выбирается метод чтения.

    - Нет лишних абстракций для маленького примера.

2. **Единая точка входа**

    - Один метод `readDocument` отвечает за чтение документов.


---

### Что **не ок** ❌ и почему

1. **Создание `DocumentReader` через `new`**

    ```java
    DocumentReader reader = new DocumentReader();
    ```

    - Нарушает Dependency Injection.

    - Нельзя подменить `DocumentReader` в тестах.

    - Если `DocumentReader` станет состоянием или тяжёлым объектом — проблемы.

2. **Возможный `NullPointerException`**

    ```java
    if (type.equals("PDF")) { ... }
    ```

    - Если `type == null`, приложение упадёт.

    - Безопаснее: `"PDF".equals(type)` или enum.

3. **Возврат `null`**

    ```java
    else {
        return null;
    }
    ```

    - Плохой контракт метода.

    - Клиент не понимает: это ошибка? тип не поддержан? файл пустой?

4. **String-based типы**

    - `"PDF"`, `"DOCX"`, `"XLSX"` — магические строки.

    - Ошибка в одном символе → баг на проде.

5. **Нарушение Open/Closed Principle (OCP)**

    - Добавление нового формата требует изменения метода и нового `else if`.

    - Код плохо масштабируется.

6. **Отсутствие явной ошибки**

    - Если тип не поддержан, лучше выбросить исключение с понятным сообщением.


---

### Как улучшить (минимально)

```java
@Component
@RequiredArgsConstructor
public class DocumentService {

    private final DocumentReader reader;

    public InputStream readDocument(String type) {
        if (type == null) {
            throw new IllegalArgumentException("Document type must not be null");
        }

        return switch (type) {
            case "PDF"  -> reader.readPdf();
            case "DOCX" -> reader.readDocx();
            case "XLSX" -> reader.readXlsx();
            default -> throw new IllegalArgumentException("Unsupported document type: " + type);
        };
    }
}
```

✔️ Уже лучше:

- DI вместо `new`.

- Нет `null`-возврата.

- Явная ошибка.


---

### Как сделать **правильно и масштабируемо** ⭐

**1. Enum типов документов**

```java
public enum DocumentType {
    PDF, DOCX, XLSX
}
```

**2. Стратегия через Map**

```java
@Component
@RequiredArgsConstructor
public class DocumentService {

    private final Map<DocumentType, Supplier<InputStream>> readers;

    public InputStream readDocument(DocumentType type) {
        Supplier<InputStream> reader = readers.get(type);
        if (reader == null) {
            throw new IllegalArgumentException("Unsupported document type: " + type);
        }
        return reader.get();
    }
}
```

**3. Конфигурация**

```java
@Configuration
public class DocumentConfig {

    @Bean
    public Map<DocumentType, Supplier<InputStream>> readers(DocumentReader reader) {
        return Map.of(
            DocumentType.PDF, reader::readPdf,
            DocumentType.DOCX, reader::readDocx,
            DocumentType.XLSX, reader::readXlsx
        );
    }
}
```

---

### Как это звучит на собеседовании (коротко)

> В текущем коде ок простота, но есть проблемы: `new` вместо DI, `null`-возврат, магические строки и нарушение OCP.  
> Я бы ввёл `enum` типов и стратегию, чтобы добавление нового формата не требовало переписывания сервиса, а также сделал явный контракт ошибок.

Если хочешь — можем упростить ответ под junior/middle или наоборот ужать под **1–2 минуты устного ответа**.

{{< /details >}}