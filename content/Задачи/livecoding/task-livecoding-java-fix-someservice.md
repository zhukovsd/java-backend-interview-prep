#### 52. Рефакторинг SomeServiceImpl для асинхронности и корректности


Дан код. Надо найти ошибки в классе и поправить. Дан высоконагруженный микросервис. Запущено в k8s 8 подов (реплик). Цель кода вычитать из БД записи и отправить в сервис аналитики. За это отвечает метод processJobRunning(). И вторая задача кода: отправить чеки в налоговую. Снаружи из другого нашего микросервиса приходит запрос sendReceipt(). Объект taxClient наподобие feign client. Налоговая может отвечать больше 10 сек, а нам нужно работать быстро. Как это порешать? Там, где написано "граница класса", там ниже есть DTO-шки, репозитории, которые помогут прочитать код. И там ошибки не надо искать




**Условие задачи:**  
📌 Даны два метода в высоконагруженном Spring‑микросервисе:
1. `processJobRunning()` — раз в час читает необработанные возвратные чеки и отправляет события в аналитику, затем сохраняет чек.
2. `sendReceipt()` — принимает DTO чека и синхронно вызывает внешний Feign‑клиент `taxClient.sendReceipt()`, который может отвечать до 60 секунд.  
   Нужно:
- Исправить ошибки в текущей реализации (ограничение AOP, некорректный вызов `findById()`, кавычки в `@Scheduled` и т.п.).
- Обеспечить, чтобы метод `sendReceipt()` не блокировал поток ожиданием ответа налоговой, а работал асинхронно.

**Код:**

```java
// Микросервис чеков высоконагруженного маркетплейса, десятки тысяч заказов в сутки.
// Основные функции микросервиса - и 30 - сохранение и отправка чеков в налоговую (во внешний независимый сервис)
// (Инфраструктура - Kubernetes 8 подов (реплик), высоконагруженный).
// Две независимые функции (процесса)
//                1. Сохранение в бд (Postgres) сведения о данных в чек и отправка в Налоговую - метод sendReceipt (в реальном времени).
//                2. Сохранение по расписанию (раз в час) чеков возврата - метод processJobRunning.
// TaxClient - клиент налоговой (feign) - timeout = 60 секунд (не наш, не можем повлиять)

@Service
@AllArgsConstructor
public class SomeServiceImpl implements Someservice {
    private final TaxClient taxClient;
    private final ReceiptDao receiptDao;
    private final ReceiptTypeDao receiptTypeDao;

    private final ReceiptMapper mapper;
    private final ReceiptTypeMapper typeMapper;
    private final RefundReceiptMapper refundReceiptMapper;
    private final SomeService self;

    @Scheduled(cron = ""${cron.expression}"") // ""30 30 * * *""
    public void processJobRunning() {
        List<Receipt> receipts = getRefundReceipts();

        for (Receipt receipt : receipts) {
            receipt.setProcessed(true);
            eventListener.sendEventToAnalytics(new SomeEvent(receipt.getId()); //send simple event, using org.springframework.context.event, idempotent
            receiptDao.save(mapReceipt(mapper.map(receipt)));
        }
    }

    @Transactional(readOnly = true)
    public List<Receipt> getRefundReceipts() {
        return receiptDao.findAllBySourceAndProcessedFalse(ReceiptSource.REFUND); // select * from receipt where source = 'REFUNC' and processed = false;
    }

    private Receipt mapReceipt(ReceiptDto receiptDto) {
        return mapper.map(receiptDto);
    }

    //приходит http-запрос из другого микросервиса
    public void sendReceipt(ReceiptDto receiptDto) {
        ReceiptSource source = self.getReceiptSource(receiptDto);
        taxClient.sendReceipt(receiptDto, source);
    }

    @Transactional
    @Cacheable(value = ""receipt_type"", cacheManager = ""redisCacheManager"") // по сути работает как hashMap
    public ReceiptSource getReceiptSource(ReceiptDto receiptDto) {
        Receipt receipt = receiptDao.findById(receiptDto.getId());
        ReceiptSource source = receipt.getSource();
        return source;
    }
}


// граница класса ----
public interface ReceiptDao extends JpaRepository<Receipt, Long> {
    List<Receipt> findAllBySourceAndProcessedFalse(ReceiptSource source);
}

//тут все корректно - для наглядности - mapstruct
@Mapper(componentModel = SPRING)
public interface ReceiptMapper {
    Receipt map(ReceiptDto dto);
    ReceiptDto map(Receipt receipt);
}

// граница класса ----
// тут все корректно - dto для наглядности
public enum ReceiptSource {
    DISCOUNT,
    SELL,
    REFUND
}

// граница класса ----
//тут все корректно - dto для наглядности
@Data
@NoArgsConstructor
public class ReceiptDto {
    private Long id;
    private String address;
    private String sum;
    private ReceiptSource source;
}

// граница класса ----
//тут все корректно - entity для наглядности
//@Data
@Entity
@ALLArgsConstructor
@NoArgsConstructor
@Data
public class Receipt {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private boolean processed;
    private String address;
    private String sum;
    private ReceiptSource source;
}
````

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 В `@Scheduled` нужно писать `cron = "${cron.expression}"`, а не двойные кавычки внутри.  
💡 AOP-прокси Spring с `@Cacheable` не срабатывает при вызове `this.getReceiptSource(...)` — надо вызывать через биновый прокси (`self.getReceiptSource(...)`).  
💡 `findById()` возвращает `Optional<Receipt>` — обязательно обрабатывать отсутствие записи.  
💡 Чтобы не блокировать поток долгим Feign‑вызовом, сделайте `sendReceipt()` асинхронным (`@Async`) или отправляйте DTO в очередь (Kafka/RabbitMQ).  
💡 При сохранении внутри цикла оборачивайте отправку события в `try/catch`, чтобы одна ошибка не прерывала весь процесс.  
{{< /details >}}

{{< details "Решение" close >}}

```java
import java.util.List;
import java.util.concurrent.Executor;
import lombok.AllArgsConstructor;
import org.springframework.cache.annotation.Cacheable;
import org.springframework.scheduling.annotation.Async;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
@AllArgsConstructor
public class SomeServiceImpl implements SomeService {
    private final TaxClient taxClient;
    private final ReceiptDao receiptDao;
    private final SomeEventListener eventListener;
    private final SomeService self;  // биновый прокси для AOP

    @Scheduled(cron = "${cron.expression}")
    public void processJobRunning() {
        List<Receipt> receipts = getRefundReceipts();
        for (Receipt receipt : receipts) {
            try {
                receipt.setProcessed(true);
                eventListener.sendEventToAnalytics(new SomeEvent(receipt.getId()));
                receiptDao.save(receipt);
            } catch (Exception ex) {
                // логируем и продолжаем
                log.error("Error processing receipt {}", receipt.getId(), ex);
            }
        }
    }

    @Transactional(readOnly = true)
    public List<Receipt> getRefundReceipts() {
        return receiptDao.findAllBySourceAndProcessedFalse(ReceiptSource.REFUND);
    }

    public void sendReceipt(ReceiptDto receiptDto) {
        // Асинхронный вызов через прокси Spring
        self.sendReceiptAsync(receiptDto);
    }

    @Async("taskExecutor")
    public void sendReceiptAsync(ReceiptDto receiptDto) {
        ReceiptSource source = getReceiptSource(receiptDto);
        taxClient.sendReceipt(receiptDto, source);
    }

    @Transactional
    @Cacheable(value = "receipt_type", cacheManager = "redisCacheManager")
    public ReceiptSource getReceiptSource(ReceiptDto receiptDto) {
        Receipt receipt = receiptDao.findById(receiptDto.getId())
            .orElseThrow(() -> new EntityNotFoundException("Receipt not found: " + receiptDto.getId()));
        return receipt.getSource();
    }
}
```

{{< /details >}}