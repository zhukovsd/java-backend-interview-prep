#### 28. Пользователи без поездок

**Условие задачи:**  
📌 Есть таблицы:  

```sql

create table users (
    id int8 primary key,
    name text,
    email text
);

create table trips (
    id int8 primary key,
    scooter int8,
    user_id int8,
    started_at timestamptz,
    finished_at timestamptz
);

create table pauses (
    id int8 primary key,
    trip_id int8,
    started_at timestamptz,
    finished_at timestamptz
);

```

Нужно вывести `id` всех пользователей, у которых нет ни одной поездки в таблице `trips`.

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Используйте `LEFT JOIN` между `users` и `trips`, фильтруя по `trips.user_id IS NULL`.  
💡 Альтернативно, примените `NOT EXISTS` с подзапросом, проверяющим отсутствие записей в `trips` для каждого пользователя.  
{{< /details >}}

{{< details "Решение" close >}}
```sql
-- Решение 1: LEFT JOIN + фильтрация по NULL
SELECT u.id
FROM users u
LEFT JOIN trips t ON u.id = t.user_id
WHERE t.user_id IS NULL;

-- Решение 2: NOT EXISTS
SELECT u.id
FROM users u
WHERE NOT EXISTS (
    SELECT 1
    FROM trips t
    WHERE t.user_id = u.id
);
```

{{< /details >}}