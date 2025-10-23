#### 85. Code Review: @Transactional + внешний HTTP вызов

**Условие задачи:**  
📌 Есть сервис, который делает внешний HTTP-вызов и сохраняет результат в БД. Нужно провести код-ревью: какие проблемы в этом коде и как их исправить.

**Код:**

```java
@Service
public class RestClient {

    @Transactional
    public void doWork() {
        var obj = restTemplate.postForObject(...);
        dbService.saveObj(obj);
    }
}
```

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Не держите транзакцию БД во время сетевого запроса — это блокирует ресурсы и увеличивает риск длительных блокировок.  
💡 Обработайте ошибки/таймауты внешнего вызова (timeouts, retries/circuit breaker, фолбэки).  
💡 Сделайте `RestTemplate`/клиент биновым и внедряйте через конструктор.  
💡 Разделите ответственность: внешний вызов отдельно, сохранение — в отдельной транзакции.  
💡 Продумайте идемпотентность: если внешний вызов прошёл, а транзакция откатилась — возможны дубли/несогласованность.  
{{< /details >}}

{{< details "Решение" close >}}

```java
@Configuration
class HttpClientConfig {
    @Bean
    RestTemplate restTemplate() {
        var factory = new HttpComponentsClientHttpRequestFactory();
        factory.setConnectTimeout(2_000);
        factory.setReadTimeout(3_000);
        return new RestTemplate(factory);
    }
}

@Service
@RequiredArgsConstructor
class RestClientService {

    private final RestTemplate restTemplate;
    private final DbService dbService;

    public void doWork() {
        // 1) Внешний HTTP-вызов ВНЕ транзакции БД
        ResponseDto obj = callRemote();

        // 2) Отдельная короткая транзакция только на запись в БД
        save(obj);
    }

    private ResponseDto callRemote() {
        try {
            // пример: добавьте идемпотентный ключ, если поддерживается провайдером
            var headers = new HttpHeaders();
            headers.add("Idempotency-Key", UUID.randomUUID().toString());
            var request = new HttpEntity<>(buildRequest(), headers);

            return restTemplate.postForObject("https://remote/api", request, ResponseDto.class);
        } catch (RestClientResponseException ex) {
            // HTTP 4xx/5xx
            throw new ExternalServiceException("Remote error: " + ex.getRawStatusCode(), ex);
        } catch (ResourceAccessException ex) {
            // таймауты/сети
            throw new ExternalServiceException("Remote timeout/unavailable", ex);
        }
    }

    @Transactional
    protected void save(ResponseDto obj) {
        // валидация данных перед сохранением
        if (obj == null) {
            throw new IllegalArgumentException("Remote response is null");
        }
        dbService.saveObj(obj);
    }

    private RequestDto buildRequest() {
        // сконструируйте DTO запроса
        return new RequestDto(/* ... */);
    }
}

/** Пример доменных исключений — не даём "прятать" сетевые ошибки как SQL */
class ExternalServiceException extends RuntimeException {
    ExternalServiceException(String msg, Throwable cause) { super(msg, cause); }
}

/** Пример DbService — транзакция может жить здесь, если хочется инкапсулировать слой */
@Service
@RequiredArgsConstructor
class DbService {
    private final ObjRepository repo;

    @Transactional(propagation = Propagation.MANDATORY)
    public void saveObj(ResponseDto obj) {
        repo.save(map(obj));
    }

    private ObjEntity map(ResponseDto dto) {
        // mapper DTO -> Entity
        return new ObjEntity(/* ... */);
    }
}
```

**Что исправлено и почему (кратко):**

- 🚫 Убрали длительную БД-транзакцию вокруг сетевого вызова: теперь транзакция короткая и только на `save(...)`.

- 🧯 Добавили таймауты и обработку ошибок для HTTP клиента, чтобы не висеть бесконечно.

- 🧱 Разделили слои и ответственности, внедрение зависимостей — через конструктор.

- 🔁 Продумана идемпотентность (Idempotency-Key) для избежания дублей при повторах.

- ✅ Явные доменные исключения: проще различать сбои внешнего сервиса и БД.


{{< /details >}}