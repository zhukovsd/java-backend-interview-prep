#### 57. Рефакторинг DocumentService с паттерном Стратегия

**Условие задачи:**  
📌 Есть класс `DocumentService` с методом `readDocument(String type)`, в котором жёстко прописан выбор типа через `if-else`. Нужно провести код‑ревью, выделить плюсы и минусы текущей реализации, и **рефакторить** под паттерн **Стратегия**, а также **добавить реализацию** для чтения PDF.

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
````

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Сейчас в `readDocument` жёсткая привязка к типам через `if-else` — при добавлении новых форматов придётся править метод.  
💡 Плюс: простой и понятный код для трёх форматов.  
💡 Минус: нарушает принцип OCP (открытость/закрытость), сложно тестировать и расширять.  
💡 Решение: выделить интерфейс `DocumentReaderStrategy`, реализовать для каждого формата и инжектить коллекцию стратегий в `DocumentService`.  
{{< /details >}}

{{< details "Решение" close >}}

```java
// 1. Определяем стратегию
public interface DocumentReaderStrategy {
    boolean supports(String type);
    InputStream read(InputStream source);
}

// 2. Реализация для PDF
@Component
@Qualifier("PDF")
public class PdfDocumentReader implements DocumentReaderStrategy {
    @Override
    public boolean supports(String type) {
        return "PDF".equalsIgnoreCase(type);
    }

    @Override
    public InputStream read(InputStream source) {
        // TODO: Реальная логика чтения PDF из source
        // Для примера просто возвращаем source
        return source;
    }
}

// 3. Сервис с коллекцией стратегий
@Service
public class DocumentService {
    private final List<DocumentReaderStrategy> readers;

    public DocumentService(List<DocumentReaderStrategy> readers) {
        this.readers = readers;
    }

    public InputStream readDocument(String type, InputStream source) {
        return readers.stream()
            .filter(r -> r.supports(type))
            .findFirst()
            .orElseThrow(() -> new IllegalArgumentException("Unsupported type: " + type))
            .read(source);
    }
}

// 4. Пример вызова
@Component
public class AppRunner implements CommandLineRunner {
    private final DocumentService documentService;

    public AppRunner(DocumentService documentService) {
        this.documentService = documentService;
    }

    @Override
    public void run(String... args) throws Exception {
        InputStream pdfSource = new ByteArrayInputStream(new byte[]{/* ... */});
        InputStream result = documentService.readDocument("PDF", pdfSource);
        System.out.println("PDF прочитан, получен InputStream: " + result);
    }
}
```

{{< /details >}}