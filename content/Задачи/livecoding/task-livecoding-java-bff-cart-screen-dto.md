#### 83. BFF: экран корзины с рекомендациями

**Условие задачи:**  
📌 Livecoding. Мы с тобой пишем свой стартап. Типо озона. Фишка нашего стартапа будет допродажа. Это когда, я как пользователь вижу новенький айфончик, кидаю его в корзину, перехожу на экран корзины и там вместе с айфоном вижу экран с рекомендациями, что подходит к моему выбранному айфону. Для того, чтобы это осуществить мы строим свой BFF. Наша задача вернуть DTO. Для задачи всегда приходят релевантные данные.

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
💡 Внедрите оба Feign-клиента в сервис через конструктор.  
💡 Получите `cart` из `ShoppingCartServiceClient#getClientCart()`.  
💡 Получите `recommendations` из `RecommendationsServiceClient#getClientRecommendations()`.  
💡 Верните `new ClientCartScreenDto(cart, recommendations)`.  
{{< /details >}}

{{< details "Решение" close >}}

```java
@Service
@RequiredArgsConstructor
public class ClientCartScreenService {

    private final ShoppingCartServiceClient cartClient;
    private final RecommendationsServiceClient recommendationsClient;

    public ClientCartScreenDto getScreenData() {
        CartDto cart = cartClient.getClientCart();
        List<RecommendationDto> recommendations = recommendationsClient.getClientRecommendations();
        return new ClientCartScreenDto(cart, recommendations);
    }
}
```

{{< /details >}}