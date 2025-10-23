#### 45. Сотрудник с максимальной зарплатой в каждом департаменте

**Условие задачи:**  
📌 Есть таблицы `departments(id, name)` и `employees(id, name, department_id, salary)`.  
Необходимо для каждого департамента вывести:
- название департамента (`department`),
- имя сотрудника (`employee`),
- его зарплату (`salary`),
  где сотрудник имеет **максимальную** зарплату в своём департаменте.

```sql
CREATE TABLE departments (
    id   INT PRIMARY KEY,
    name VARCHAR(100)
);

CREATE TABLE employees (
    id             INT PRIMARY KEY,
    name           VARCHAR(100),
    department_id  INT REFERENCES departments(id),
    salary         NUMERIC(10,2)
);
````

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Используйте оконную функцию `ROW_NUMBER()` или `RANK()` для нумерации сотрудников в каждом департаменте по убыванию зарплаты.  
💡 В подзапросе или CTE вычислите номер строки в разрезе `department_id`, сортируя по `salary DESC`.  
💡 Затем выберите только те записи, где номер строки = 1.  
{{< /details >}}

{{< details "Решение" close >}}

```sql
WITH ranked_emps AS (
    SELECT
        d.name AS department,
        e.name AS employee,
        e.salary,
        ROW_NUMBER() OVER (
            PARTITION BY e.department_id 
            ORDER BY e.salary DESC
        ) AS rn
    FROM employees e
    JOIN departments d
      ON e.department_id = d.id
)
SELECT
    department,
    employee,
    salary
FROM ranked_emps
WHERE rn = 1
ORDER BY department;
```

{{< /details >}}