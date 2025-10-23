#### 29. Самые повторяющиеся значения `value`

**Условие задачи:**  
📌 Есть таблица с колонками `key` и `value`:
```text
key | value
----+-------
1   | sdfs
2   | dsfg
3   | sdfs
```

Необходимо найти самое часто встречающееся значение в колонке `value`.

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Используйте `GROUP BY value` для подсчёта количества вхождений каждого значения.  
💡 Отсортируйте результат по убыванию счётчика (`COUNT(*) DESC`) и возьмите первую строку (`LIMIT 1`).  
💡 Альтернативно примените оконную функцию `ROW_NUMBER()` или `RANK()`, чтобы выбрать значение с максимальным количеством.  
{{< /details >}}

{{< details "Решение" close >}}

```sql
-- Решение 1: GROUP BY + ORDER BY + LIMIT
SELECT value
FROM your_table
GROUP BY value
ORDER BY COUNT(*) DESC
LIMIT 1;

-- Решение 2: Оконная функция RANK()
SELECT value
FROM (
    SELECT
        value,
        RANK() OVER (ORDER BY COUNT(*) DESC) AS rnk
    FROM your_table
    GROUP BY value
) t
WHERE rnk = 1;
```

{{< /details >}}