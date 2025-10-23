#### 52. Минимальная и максимальная зарплата по отделам среди неуволенных

**Условие задачи:**  
📌 В базе данных есть две таблицы: "units" – подразделение компании и "employees" – сотрудники компании.
units:
id primary key
name (название unit)
employees:
id primary key
unit_id foreign key -> units::id
salary (зарплата)
fired (флаг уволен true, false)

Необходимо написать sql запрос, который вернет минимальную
и максимальную зарплату по каждому отделу среди неуволенных сотрудников. (unit_id)


{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Отберитесь по признаку неуволенных: `WHERE fired = false` (или `= 0`, если BOOL хранится как int).  
💡 Сгруппируйтесь по отделу: `GROUP BY unit_id` (и `u.name`, если нужен человекочитаемый вывод).  
💡 Агрегируйте зарплаты: `MIN(salary)`, `MAX(salary)`.  
{{< /details >}}

{{< details "Решение" close >}}

```sql
-- Вариант 1: по идентификатору отдела
SELECT
    e.unit_id,
    MIN(e.salary) AS min_salary,
    MAX(e.salary) AS max_salary
FROM employees e
WHERE e.fired = false
GROUP BY e.unit_id;
```

```sql
-- Вариант 2: с названием отдела
SELECT
    u.id   AS unit_id,
    u.name AS unit_name,
    MIN(e.salary) AS min_salary,
    MAX(e.salary) AS max_salary
FROM units u
JOIN employees e
  ON e.unit_id = u.id
 AND e.fired = false
GROUP BY u.id, u.name;
```

> Примечание: если хотите показывать **все** отделы даже без активных сотрудников, используйте `LEFT JOIN` и переносите условие `fired = false` в `ON`, а не в `WHERE`:

```sql
SELECT
    u.id   AS unit_id,
    u.name AS unit_name,
    MIN(e.salary) AS min_salary,
    MAX(e.salary) AS max_salary
FROM units u
LEFT JOIN employees e
  ON e.unit_id = u.id
 AND e.fired = false
GROUP BY u.id, u.name;
-- Для отделов без неуволенных сотрудников min/max будут NULL
```

{{< /details >}}