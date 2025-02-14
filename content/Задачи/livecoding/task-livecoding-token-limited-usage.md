#### 9. Реализовать сервис для работы с токенами с ограничением на количество использований?


{{< hint warning >}}
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}

- Для реализации сервиса с токенами нужно создать структуру данных, которая будет отслеживать количество использований токенов.
- Можно использовать **HashMap** или **Redis** для хранения токенов с их статусами.
- Нужно хранить:
    - Токен (например, строка).
    - Количество оставшихся использований для этого токена.
- После использования токена количество оставшихся использований уменьшается.
- Если количество использований исчерпано, токен становится невалидным.
- Пример ограничения использования токенов:
    - Токен может быть использован 5 раз.
    - После пятого использования токен становится недействительным.
{{< /details >}}

{{< details "Решение" close >}}

Пример реализации сервиса для работы с токенами:

```java
import java.util.HashMap;
import java.util.Map;

@Service
public class TokenService {

    private final Map<String, Token> tokenStorage = new HashMap<>();
    private final int maxUses = 5; // Максимальное количество использований токена

    // Метод для создания токена
    public String generateToken() {
        String token = "token-" + System.currentTimeMillis();
        tokenStorage.put(token, new Token(token, maxUses));
        return token;
    }

    // Метод для использования токена
    public boolean useToken(String token) {
        Token tokenData = tokenStorage.get(token);
        if (tokenData == null || tokenData.getRemainingUses() <= 0) {
            return false; // Токен не существует или исчерпано количество использований
        }
        
        // Уменьшаем количество оставшихся использований
        tokenData.decreaseRemainingUses();
        return true;
    }

    // Метод для проверки количества оставшихся использований
    public int getRemainingUses(String token) {
        Token tokenData = tokenStorage.get(token);
        if (tokenData == null) {
            return 0; // Токен не найден
        }
        return tokenData.getRemainingUses();
    }

    // Вспомогательный класс для представления токена
    private static class Token {
        private final String token;
        private int remainingUses;

        public Token(String token, int remainingUses) {
            this.token = token;
            this.remainingUses = remainingUses;
        }

        public String getToken() {
            return token;
        }

        public int getRemainingUses() {
            return remainingUses;
        }

        public void decreaseRemainingUses() {
            this.remainingUses--;
        }
    }
}
```

 **Описание работы:**

- В **TokenService** используется **Map** для хранения токенов, где ключом является сам токен, а значением — объект **Token**, который содержит информацию о токене и количестве оставшихся использований.
- Метод **generateToken()** генерирует новый токен с максимальным количеством использований.
- Метод **useToken(String token)** проверяет, был ли токен использован меньше максимального числа раз, и если да, то уменьшает оставшееся количество использований. Если токен исчерпан или не существует, метод возвращает **false**.
- Метод **getRemainingUses(String token)** возвращает количество оставшихся использований для токена.
- Вспомогательный класс **Token** инкапсулирует данные о токене и реализует логику уменьшения оставшихся использований.

 **Пример использования:**

```java
public class Main {
    public static void main(String[] args) {
        TokenService tokenService = new TokenService();
        
        // Генерация токена
        String token = tokenService.generateToken();
        System.out.println("Generated token: " + token);

        // Проверка оставшихся использований
        System.out.println("Remaining uses: " + tokenService.getRemainingUses(token));

        // Использование токена несколько раз
        for (int i = 0; i < 6; i++) {
            boolean result = tokenService.useToken(token);
            System.out.println("Use token " + (i+1) + ": " + result);
        }

        // Проверка оставшихся использований после исчерпания
        System.out.println("Remaining uses after attempts: " + tokenService.getRemainingUses(token));
    }
}
```

 **Ключевые моменты:**

- **HashMap** используется для хранения токенов и их состояний (сколько раз их можно использовать).
- При каждом использовании токена уменьшается его количество использований.
- **Token** — это вспомогательный класс, который инкапсулирует логику работы с токенами.
- Если количество использований токена исчерпано, токен становится невалидным.
- Можно добавить дополнительную логику для удаления неактивных токенов по истечении срока действия или по количеству использований.
{{< /details >}}
