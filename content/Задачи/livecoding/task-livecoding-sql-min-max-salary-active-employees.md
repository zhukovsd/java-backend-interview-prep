#### 56. Минимальная и максимальная зарплата по отделам среди неуволенных сотрудников


**Условие задачи:**  
📌 Даны таблицы `units` и `employees`.  
Нужно написать SQL-запрос, который вернёт:

- ID отдела,

- минимальную зарплату,

- максимальную зарплату  
  — среди сотрудников, у которых `fired = false`.


**Структура таблиц:**

```
units:
  id int PK
  name text

employees:
  id int PK
  unit_id int FK → units.id
  salary numeric
  fired boolean
```

---

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

---

{{< details "Подсказки" close >}}  
💡 Делаем `JOIN` таблиц по `unit_id`.  
💡 Фильтруем только `fired = false`.  
💡 Группируем по отделам (`GROUP BY`).  
💡 Используем `MIN()` и `MAX()` для зарплат.  
💡 Если нужно вернуть _все отделы_, используйте `LEFT JOIN`.  
{{< /details >}}

---

{{< details "Решение" close >}}

```sql
SELECT
    u.id AS unit_id,
    u.name AS unit_name,
    MIN(e.salary) AS min_salary,
    MAX(e.salary) AS max_salary
FROM units u
JOIN employees e
  ON e.unit_id = u.id
 AND e.fired = false
GROUP BY u.id, u.name;
```

**Альтернативный вариант (если нужно показывать даже отделы без сотрудников):**

```sql
SELECT
    u.id AS unit_id,
    u.name AS unit_name,
    MIN(e.salary) AS min_salary,
    MAX(e.salary) AS max_salary
FROM units u
LEFT JOIN employees e
  ON e.unit_id = u.id
 AND e.fired = false
GROUP BY u.id, u.name;
```

{{< /details >}}