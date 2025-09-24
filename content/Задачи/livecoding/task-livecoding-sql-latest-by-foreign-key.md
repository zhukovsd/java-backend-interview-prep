#### 51. Последняя запись по каждому col2

🔥 Есть таблица table1. В ней 4 колонки. col1 это первичный ключ. col2 это secondary_id (либо foreign key, мы знаем, что он связан с другой таблицей и не уникальный в этой таблице). col3 это amount (денежное состояние). col4 это системное поле date (change date, когда изменилось). Написать SELECT запрос, который для каждого разного col2 возвращает только строку, у которой самая последняя свежая date

```text
table1
col1 pk
col2 sec_fk
col3 amount
col4 date
```

---

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Удобно использовать оконную функцию `ROW_NUMBER()` с разбиением по `col2` и сортировкой по `col4 DESC`.  
💡 В PostgreSQL есть короткий вариант с `DISTINCT ON (col2)` и `ORDER BY col2, col4 DESC`.  
💡 Добавьте индекс на `(col2, col4 DESC)` для ускорения.  
{{< /details >}}

{{< details "Решение" close >}}  
Вариант 1 (ANSI SQL, универсальный, через оконную функцию):

```sql
SELECT col1, col2, col3, col4
FROM (
  SELECT
    t.*,
    ROW_NUMBER() OVER (PARTITION BY col2 ORDER BY col4 DESC) AS rn
  FROM table1 t
) s
WHERE s.rn = 1
ORDER BY col2;
```

Вариант 2 (PostgreSQL, `DISTINCT ON`):

```sql
SELECT DISTINCT ON (t.col2)
  t.col1, t.col2, t.col3, t.col4
FROM table1 t
ORDER BY t.col2, t.col4 DESC;
```

{{< /details >}}