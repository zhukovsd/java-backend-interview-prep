#### 94. REST эндпоинт: получить все записи из таблицы Demands

**Условие задачи:**  
📌 В базе есть таблица `Demands`. Фронт-разработчик просит сделать REST-эндпоинт, который вернёт **все строки** из этой таблицы.

```text
Demands
+----+-------+--------+
| id | name  | status |
+----+-------+--------+
| 1  | name1 | GOOD   |
| 2  | name2 |        |
| 3  | name3 |        |
| 4  | name4 |        |
+----+-------+--------+
```

Нужно написать минимальный набор кода (Entity + Repository + Controller), чтобы по HTTP-запросу фронт получил список всех `Demands`.

---

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Определи сущность `Demand` с полями `id`, `name`, `status` и пометь её `@Entity`.  
💡 Создай `DemandRepository`, унаследовавшись от `JpaRepository<Demand, Long>`.  
💡 В `@RestController` внедри репозиторий и сделай `@GetMapping("/api/v1/demands")`, который вернёт `List<Demand>`.  
💡 Возвращать можно прямо сущность, либо `DTO`, если хочешь отделить слои. Для задачи достаточно сущности.  
{{< /details >}}

---

{{< details "Решение" close >}}

```java
// Сущность, соответствующая таблице Demands
import jakarta.persistence.*;

@Entity
@Table(name = "demands")
public class Demand {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    private String status;

    public Demand() {
    }

    public Demand(Long id, String name, String status) {
        this.id = id;
        this.name = name;
        this.status = status;
    }

    public Long getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public String getStatus() {
        return status;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setStatus(String status) {
        this.status = status;
    }
}
```

```java
// Репозиторий для работы с таблицей Demands
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface DemandRepository extends JpaRepository<Demand, Long> {
    // findAll() уже есть в JpaRepository
}
```

```java
// REST-контроллер, отдающий все записи Demands
import org.springframework.web.bind.annotation.*;
import lombok.RequiredArgsConstructor;

import java.util.List;

@RestController
@RequestMapping("/api/v1/demands")
@RequiredArgsConstructor
public class DemandController {

    private final DemandRepository demandRepository;

    @GetMapping
    public List<Demand> getAllDemands() {
        return demandRepository.findAll();
    }
}
```

Теперь фронтендер может сделать запрос:

```http
GET /api/v1/demands
```

И получить JSON примерно такого вида:

```json
[
  { "id": 1, "name": "name1", "status": "GOOD" },
  { "id": 2, "name": "name2", "status": "" },
  { "id": 3, "name": "name3", "status": "" },
  { "id": 4, "name": "name4", "status": "" }
]
```

---

Если захочешь — можем следующим шагом добавить DTO, фильтрацию по статусу или пагинацию.

{{< /details >}}