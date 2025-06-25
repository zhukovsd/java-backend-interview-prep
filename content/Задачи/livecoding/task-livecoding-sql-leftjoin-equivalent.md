#### 42. Эквивалент LEFT OUTER JOIN без использования OUTER JOIN

**Условие задачи:**  
📌 Есть две таблицы `t1(A)` и `t2(B)`.  
Исходный запрос:
```sql
SELECT *
FROM t1
LEFT OUTER JOIN t2
  ON t1.A = t2.B;
````

Необходимо переписать его эквивалентно **без** использования `LEFT OUTER JOIN`.

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Разбейте на два подзапроса:

1. **INNER JOIN** для совпадающих строк.

2. Строки из `t1`, для которых нет совпадений в `t2`.  
   💡 Объедините их с помощью `UNION ALL`.  
   💡 Для второго подзапроса используйте `NOT EXISTS` или `NOT IN`.  
   {{< /details >}}


{{< details "Решение" close >}}

```sql
-- Вариант 1: INNER JOIN + UNION ALL + NOT EXISTS
SELECT t1.A, t2.B
FROM t1
JOIN t2
  ON t1.A = t2.B

UNION ALL

SELECT t1.A, NULL AS B
FROM t1
WHERE NOT EXISTS (
    SELECT 1
    FROM t2
    WHERE t2.B = t1.A
);
```

```sql
-- Вариант 2: INNER JOIN + UNION ALL + NOT IN
SELECT t1.A, t2.B
FROM t1
JOIN t2
  ON t1.A = t2.B

UNION ALL

SELECT t1.A, NULL AS B
FROM t1
WHERE t1.A NOT IN (
    SELECT t2.B
    FROM t2
);
```

{{< /details >}}