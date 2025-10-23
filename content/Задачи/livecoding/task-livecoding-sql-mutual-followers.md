#### 35. Взаимные подписки (mutual follows)

**Условие задачи:**  
📌 Есть таблица подписок в соцсети:
```

user_id | follower_id  
--------+------------  
1 | 5  
1 | 4  
2 | 3  
3 | 2  
5 | 1

```

Нужно найти пары пользователей, которые **фоловят друг друга** (не транзитивно), и вывести `(user_id, follower_id)`.

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Используйте **само-джойн** таблицы на условии обратной подписки: `t1.user_id = t2.follower_id AND t1.follower_id = t2.user_id`.  
💡 Альтернативно можно применить двойной `EXISTS`: проверка, что для данной пары существует обратная запись.  
💡 Не забудьте исключить дубликаты, чтобы не выводить одну и ту же пару дважды (например, `(5,1)` и `(1,5)` оба не нужны).  
{{< /details >}}

{{< details "Решение" close >}}
```sql
-- Решение 1: SELF JOIN
SELECT DISTINCT t1.user_id,
       t1.follower_id
FROM follows t1
JOIN follows t2
  ON t1.user_id = t2.follower_id
 AND t1.follower_id = t2.user_id
WHERE t1.user_id < t1.follower_id;

-- Решение 2: EXISTS
SELECT f1.user_id,
       f1.follower_id
FROM follows f1
WHERE EXISTS (
    SELECT 1
    FROM follows f2
    WHERE f2.user_id = f1.follower_id
      AND f2.follower_id = f1.user_id
)
AND f1.user_id < f1.follower_id;
```

{{< /details >}}