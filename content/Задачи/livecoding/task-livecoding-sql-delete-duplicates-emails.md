#### 53. Удаление дублей из таблицы emails

**Условие задачи:**  
📌 В таблице `emails(id, email)` могут встречаться **дублирующиеся email-адреса**.  
Нужно написать SQL-запрос, который удаляет дубликаты, оставляя только **одну запись** для каждого уникального email.

**Код:**

```sql
CREATE TABLE emails (
    id INT,
    email VARCHAR(30)
);

INSERT INTO emails(id, email)
VALUES
(1, 'aa@yandex.ru'),
(2, 'bb@yandex.ru'),
(3, 'aa@yandex.ru');
```

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Чтобы удалить дубли, сначала нужно определить, какая запись считается "уникальной" — например, с **минимальным id**.  
💡 Можно использовать **CTE** с `ROW_NUMBER()` или подзапрос с `MIN(id)`.  
💡 Убедитесь, что удаляете только дубли, а не все строки.  
{{< /details >}}

{{< details "Решение" close >}}

```sql
-- ✅ Вариант 1. Через CTE и ROW_NUMBER()
WITH duplicates AS (
    SELECT 
        id,
        ROW_NUMBER() OVER (PARTITION BY email ORDER BY id) AS rn
    FROM emails
)
DELETE FROM emails
WHERE id IN (
    SELECT id FROM duplicates WHERE rn > 1
);
```

```sql
-- ✅ Вариант 2. Без CTE (через подзапрос)
DELETE FROM emails
WHERE id NOT IN (
    SELECT MIN(id)
    FROM emails
    GROUP BY email
);
```

> 🧠 **Пояснение:**
>
> - `GROUP BY email` группирует одинаковые адреса.
>
> - `MIN(id)` оставляет один экземпляр (с наименьшим id).
>
> - Все остальные строки с тем же email удаляются.
>

После выполнения останется:

|id|email|
|---|---|
|1|[aa@yandex.ru](mailto:aa@yandex.ru)|
|2|[bb@yandex.ru](mailto:bb@yandex.ru)|


{{< /details >}}
