#### 26. Рыбы с суммарным уловом на дату d менее n


**Условие задачи:**  
📌 Есть таблицы:  
- **fish** (`id`, `name`)  
- **catch** (`id`, `fish_id`, `dt`, `quantity`)  

Необходимо вывести **наименования рыб**, для которых **суммарный улов** на указанную дату `d` **меньше** чем `n`.

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Фильтр по дате делается в `WHERE dt = :d`.  
💡 Суммарный улов считаем через `SUM(quantity)` и группировку по `fish_id`.  
💡 Используйте `HAVING SUM(quantity) < :n` для первой реализации.  
💡 Во втором варианте можно применить **подзапрос** либо **LEFT JOIN** и фильтрацию по агрегату.  
{{< /details >}}

{{< details "Решение" close >}}
-- Решение 1: GROUP BY + HAVING  
```sql
SELECT f.name
FROM fish f
JOIN catch c ON f.id = c.fish_id
WHERE c.dt = :d
GROUP BY f.name
HAVING SUM(c.quantity) < :n;
```

-- Решение 2: Подзапрос в WHERE

```sql
SELECT f.name
FROM fish f
WHERE (
    SELECT COALESCE(SUM(c.quantity), 0)
    FROM catch c
    WHERE c.fish_id = f.id
      AND c.dt = :d
) < :n;
```

{{< /details >}}