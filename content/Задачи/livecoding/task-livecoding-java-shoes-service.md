#### 20. Разработка сервиса поиска обуви

**Условие задачи:**  
👞 **Разработай сервис для поиска обуви!**  
Тебе нужно создать **REST API** с эндпоинтом `POST /api/v1/search`, который принимает запрос с фильтрами и возвращает список подходящих товаров.

🔍 **Фильтрация обуви:**

- **Сапоги** → цвет **красный** или **зелёный**, цена **от 5000 до 20000**.
- **Туфли** → цена **меньше 10000**.

📩 **Пример запроса:**

```json
{
    "type": "сапоги",
    "color": ["красный", "зелёный"],
    "minPrice": 5000,
    "maxPrice": 20000
}
```

📤 **Пример ответа:**

```json
[
    {
        "type": "сапоги",
        "color": "красный",
        "price": 15000,
        "url": "https://example.com/shoe1"
    },
    {
        "type": "сапоги",
        "color": "зелёный",
        "price": 18000,
        "url": "https://example.com/shoe2"
    }
]
```



{{< hint warning >}}
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}
💡 **Используй Spring Boot + Spring Data JPA** для работы с БД.  
💡 **Создай сущность `Shoe`** с полями `type`, `color`, `price`, `url`.  
💡 **Напиши DTO-классы** для запроса и ответа.  
💡 **Используй `JpaRepository`** и напиши кастомный метод для поиска.  
💡 **Контроллер должен принимать JSON-запрос** и возвращать список обуви.
{{< /details >}}

{{< details "Решение" close >}}

📌 **1. Создаём сущность `Shoe`**

```java
import jakarta.persistence.*;
import lombok.Data;

@Entity
@Data
public class Shoe {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String type;
    private String color;
    private Double price;
    private String url;
}
```

📌 **2. Создаём DTO для запроса**

```java
import lombok.Data;
import java.util.List;

@Data
public class ShoeSearchRequest {
    private String type;
    private List<String> color;
    private Double minPrice;
    private Double maxPrice;
}
```

📌 **3. Создаём DTO для ответа**

```java
import lombok.Data;

@Data
public class ShoeResponse {
    private String type;
    private String color;
    private Double price;
    private String url;
}
```

📌 **4. Репозиторий с кастомным запросом**

```java
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;
import java.util.List;

public interface ShoeRepository extends JpaRepository<Shoe, Long> {

    @Query("SELECT s FROM Shoe s WHERE " +
           "(:type IS NULL OR s.type = :type) AND " +
           "(COALESCE(:colors, NULL) IS NULL OR s.color IN :colors) AND " +
           "(s.price BETWEEN :minPrice AND :maxPrice)")
    List<Shoe> searchShoes(@Param("type") String type,
                           @Param("colors") List<String> colors,
                           @Param("minPrice") Double minPrice,
                           @Param("maxPrice") Double maxPrice);
}
```

📌 **5. Сервисный слой**

```java
import org.springframework.stereotype.Service;
import java.util.List;
import java.util.stream.Collectors;

@Service
public class ShoeService {
    private final ShoeRepository shoeRepository;

    public ShoeService(ShoeRepository shoeRepository) {
        this.shoeRepository = shoeRepository;
    }

    public List<ShoeResponse> searchShoes(ShoeSearchRequest request) {
        List<Shoe> shoes = shoeRepository.searchShoes(
                request.getType(),
                request.getColor(),
                request.getMinPrice() != null ? request.getMinPrice() : 0,
                request.getMaxPrice() != null ? request.getMaxPrice() : Double.MAX_VALUE
        );

        return shoes.stream().map(shoe -> {
            ShoeResponse response = new ShoeResponse();
            response.setType(shoe.getType());
            response.setColor(shoe.getColor());
            response.setPrice(shoe.getPrice());
            response.setUrl(shoe.getUrl());
            return response;
        }).collect(Collectors.toList());
    }
}
```

📌 **6. Контроллер**

```java
import org.springframework.web.bind.annotation.*;
import java.util.List;

@RestController
@RequestMapping("/api/v1/search")
public class ShoeController {
    private final ShoeService shoeService;

    public ShoeController(ShoeService shoeService) {
        this.shoeService = shoeService;
    }

    @PostMapping
    public List<ShoeResponse> searchShoes(@RequestBody ShoeSearchRequest request) {
        return shoeService.searchShoes(request);
    }
}
```

---

🚀 **Что получилось?**  
✅ **Чистая архитектура**: Контроллер, сервис, репозиторий, DTO.  
✅ **Гибкий поиск**: Можно искать не только по заданным условиям, но и добавлять новые фильтры.  
✅ **Использование `Spring Data JPA`** для удобного запроса к БД.
{{< /details >}}