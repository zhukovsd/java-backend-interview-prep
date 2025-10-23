#### 84. Ускорение BFF: параллельные вызовы корзины и рекомендаций

**Условие задачи:**  
📌 Наш BFF обращается к двум сервисам: `cart-service` (0.7s) и `recommendations-service` (0.5s). Последовательный вызов даёт >1s. Требование бизнеса — уложиться **менее чем в 1 секунду**. Нужно предложить решение и показать, как реализовать его в коде.

**Код:**

```java
@FeignClient(name = "cart-service", url = "http://shopping-cart")
public interface ShoppingCartServiceClient {
    @GetMapping("/api/v1/shopping-cart")
    CartDto getClientCart();
}

@FeignClient(name = "recommendations-service", url = "http://recommendations")
public interface RecommendationsServiceClient {
    @GetMapping("/api/v1/recommendations")
    List<RecommendationDto> getClientRecommendations();
}

public record ClientCartScreenDto(CartDto cart, List<RecommendationDto> recommendations) {}

@Service
public class ClientCartScreenService {

    public ClientCartScreenDto getScreenData() {

        // todo: вызвать свои сервисы здесь

        return new ClientCartScreenDto(cart, recommendations);
    }
}
```

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Делайте вызовы **параллельно**: `CompletableFuture.supplyAsync(...)` и `CompletableFuture.allOf(...)`.  
💡 Введите **timeouts** и **фолбэки** (например, пустые рекомендации при истечении времени), чтобы стабильно укладываться < 1s.  
💡 Дополнительно можно применить **Resilience4j** (bulkhead/circuit breaker), а также **кэширование** рекомендаций.  
{{< /details >}}

{{< details "Решение" close >}}

```java
@Service
@RequiredArgsConstructor
public class ClientCartScreenService {

    private final ShoppingCartServiceClient cartClient;
    private final RecommendationsServiceClient recommendationsClient;

    public ClientCartScreenDto getScreenData() {
        // Параллельные вызовы
        CompletableFuture<CartDto> cartFuture =
            CompletableFuture.supplyAsync(cartClient::getClientCart);

        CompletableFuture<List<RecommendationDto>> recsFuture =
            CompletableFuture.supplyAsync(recommendationsClient::getClientRecommendations)
                // Жёсткий таймаут на рекомендации, чтобы не вылетать за 1s SLA
                .orTimeout(700, java.util.concurrent.TimeUnit.MILLISECONDS)
                // Фолбэк: при таймауте/ошибке — пустой список рекомендаций
                .exceptionally(ex -> Collections.emptyList());

        // Ждём оба результата
        CompletableFuture.allOf(cartFuture, recsFuture).join();

        CartDto cart = cartFuture.join(); // если корзина упадёт — пусть упадёт явно
        List<RecommendationDto> recommendations = recsFuture.join();

        return new ClientCartScreenDto(cart, recommendations);
    }
}
```

{{< /details >}}