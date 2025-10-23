#### 34. Пользователи с более чем одной покупкой в день

**Условие задачи:**  
📌 Есть таблица `T1(date, user_id)` с датами покупок пользователей.  
Необходимо найти все случаи, когда пользователь совершил **более одной** покупки **в тот же день**, и вывести пары `(date, user_id)`.

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Используйте `GROUP BY date, user_id` для агрегации покупок каждого пользователя по дате.  
💡 Примените `HAVING COUNT(*) > 1` для фильтрации только тех групп, где покупок больше одной.  
💡 Альтернативно — оконная функция `COUNT(*) OVER (PARTITION BY date, user_id)` и фильтрация по этому полю.  
{{< /details >}}

{{< details "Решение" close >}}
```sql
-- Решение 1: GROUP BY + HAVING
SELECT 
    date,
    user_id
FROM T1
GROUP BY 
    date, 
    user_id
HAVING COUNT(*) > 1;

-- Решение 2: Оконная функция
SELECT DISTINCT
    date,
    user_id
FROM (
    SELECT
        date,
        user_id,
        COUNT(*) OVER (PARTITION BY date, user_id) AS cnt
    FROM T1
) sub
WHERE cnt > 1;
```

{{< /details >}}