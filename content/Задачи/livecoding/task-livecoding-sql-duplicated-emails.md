#### 41. Поиск дублированных email

**Условие задачи:**  
📌 Есть таблица `Employee(id, email, salary)`, в которой некоторые `email` повторяются. Нужно найти все `email`, встречающиеся более одного раза.

```text
Employee table with columns: id, email, salary. 
We have duplicated emails. How could we find all duplicated emails?


email
-----
a
b
c
a
```


{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Используйте `GROUP BY email` и фильтрацию `HAVING COUNT(*) > 1`.  
💡 Альтернативно можно применить оконную функцию `COUNT() OVER (PARTITION BY email)` для подсчёта вхождений.  
{{< /details >}}

{{< details "Решение" close >}}
```sql
-- Вариант 1: GROUP BY + HAVING
SELECT email
FROM Employee
GROUP BY email
HAVING COUNT(*) > 1;

-- Вариант 2: Оконная функция + DISTINCT
SELECT DISTINCT email
FROM (
    SELECT
        email,
        COUNT(*) OVER (PARTITION BY email) AS cnt
    FROM Employee
) sub
WHERE cnt > 1;
````

{{< /details >}}