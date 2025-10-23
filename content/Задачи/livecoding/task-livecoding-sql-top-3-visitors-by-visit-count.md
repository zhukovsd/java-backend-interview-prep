#### 22. Топ-3 посетителя по числу визитов

**Условие задачи:**  
📌 Есть таблица `visits(visit_id, visitor_name, timestamp)`.  
Необходимо вывести имена трёх посетителей, у которых наибольшее количество записей в таблице (визитов).

{{< hint warning >}}
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}
💡 Используйте `GROUP BY visitor_name` для подсчёта визитов по каждому человеку.  
💡 Отсортируйте результат по убыванию количества визитов (`COUNT(*) DESC`).  
💡 Примените `LIMIT 3` для выборки трех записей.  
💡 Альтернативно можно воспользоваться оконной функцией `ROW_NUMBER()` или `RANK()`.  
{{< /details >}}

{{< details "Решение" close >}}

```sql
-- Решение 1: GROUP BY + ORDER BY + LIMIT
SELECT
  visitor_name,
  COUNT(*) AS visit_count
FROM visits
GROUP BY visitor_name
ORDER BY visit_count DESC
LIMIT 3;
```

```sql
-- Решение 2: Оконная функция ROW_NUMBER()
SELECT visitor_name, visit_count
FROM (
  SELECT
    visitor_name,
    COUNT(*) OVER (PARTITION BY visitor_name) AS visit_count,
    ROW_NUMBER() OVER (
      PARTITION BY visitor_name
      ORDER BY COUNT(*) OVER (PARTITION BY visitor_name) DESC
    ) AS rn
  FROM visits
) t
WHERE rn = 1
GROUP BY visitor_name, visit_count
ORDER BY visit_count DESC
LIMIT 3;
```

{{< /details >}}