#### 28. Имена, встречающиеся более 5 раз

**Условие задачи:**  
📌 Есть таблица `students(name text, "group" text, cource bigint)` в PostgreSQL.  
Необходимо посчитать и вывести те `name`, которые в таблице встречаются более 5 раз.

{{< hint warning >}}
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}
💡 Используйте `GROUP BY name` для агрегирования по имени.  
💡 Примените `HAVING COUNT(*) > 5` для фильтрации групп с более чем 5 вхождениями.  
💡 Альтернативное решение — воспользоваться оконной функцией `COUNT(*) OVER (PARTITION BY name)` и обернуть в подзапрос с фильтром.
{{< /details >}}

{{< details "Решение" close >}}
```sql
-- Решение 1: GROUP BY + HAVING
SELECT name
FROM students
GROUP BY name
HAVING COUNT(*) > 5;

-- Решение 2: Оконная функция + DISTINCT
SELECT DISTINCT name
FROM (
    SELECT
        name,
        COUNT(*) OVER (PARTITION BY name) AS name_count
    FROM students
) t
WHERE name_count > 5;
```

{{< /details >}}