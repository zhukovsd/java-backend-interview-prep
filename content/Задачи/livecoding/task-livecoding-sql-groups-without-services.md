#### 25. Группы без услуг

**Условие задачи:**  
📌 Есть таблицы:  
- **Services** (`id`, `name`, `cost`, `price`, `group_id`)  
- **Groups** (`id`, `name`)  

Необходимо вывести **названия групп**, в которых **нет ни одной услуги**.

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Используйте **LEFT JOIN** между `Groups` и `Services`, а в `WHERE` проверяйте `NULL` в полях `Services`.  
💡 Альтернативно, примените **NOT EXISTS** с подзапросом, проверяющим отсутствие услуг в группе.  
{{< /details >}}

{{< details "Решение" close >}}
```sql
-- Решение 1: LEFT JOIN + фильтрация по NULL  
SELECT g.name  
FROM Groups g  
LEFT JOIN Services s ON g.id = s.group_id  
WHERE s.id IS NULL;

-- Решение 2: NOT EXISTS  
SELECT g.name  
FROM Groups g  
WHERE NOT EXISTS (  
    SELECT 1  
    FROM Services s  
    WHERE s.group_id = g.id  
);
```

{{< /details >}}

