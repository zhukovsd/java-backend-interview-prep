#### 31. Выбор пяти верхних записей

**Условие задачи:**  
📌 Есть таблица с колонками `key` и `value`:  
```text
key | value
----+-------
1   | sdfs
2   | dsfg
3   | sdfs
…   | …
```

Необходимо выбрать **первые 5 записей** из этой таблицы.

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 В MySQL и PostgreSQL используйте `LIMIT 5`.  
💡 В стандарте SQL можно применить `FETCH FIRST 5 ROWS ONLY`.  
💡 В SQL Server используется `TOP 5`.  
{{< /details >}}

{{< details "Решение" close >}}

```sql
-- Решение 1 (MySQL, PostgreSQL):
SELECT *
FROM your_table
LIMIT 5;

-- Решение 2 (ANSI SQL):
SELECT *
FROM your_table
FETCH FIRST 5 ROWS ONLY;

-- Решение 3 (SQL Server):
SELECT TOP 5 *
FROM your_table;
```

{{< /details >}}