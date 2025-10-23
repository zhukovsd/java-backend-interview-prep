#### 42. Число сотрудников с одинаковой зарплатой в каждом департаменте

**Условие задачи:**  
📌 Есть таблицы `department(id, name)` и `employee(id, surname, name, salary, department_id)`.  
Необходимо вывести для каждого департамента и каждой зарплаты количество сотрудников, имеющих эту зарплату.

```sql
CREATE TABLE department (
    id   BIGSERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL
);

CREATE TABLE employee (
    id             BIGSERIAL,
    surname        VARCHAR(255) NOT NULL,
    name           VARCHAR(255) NOT NULL,
    salary         BIGINT NOT NULL,
    department_id  BIGINT NOT NULL REFERENCES department(id)
);
````

**Желаемый результат:**

```
dep_name     | salary | count
-------------+--------+------
Dep_name_1   |   1000 |    3
Dep_name_1   |   2000 |    4
Dep_name_2   |   2000 |   30
```

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Соедините `department` и `employee` по `department_id`.  
💡 Используйте `GROUP BY d.name, e.salary` и `COUNT(*)`.  
💡 В альтернативном варианте можно применить оконную функцию `COUNT(*) OVER (PARTITION BY department_id, salary)`.  
{{< /details >}}

{{< details "Решение" close >}}

```sql
-- Решение 1: GROUP BY
SELECT
    d.name   AS dep_name,
    e.salary,
    COUNT(*) AS count
FROM department d
JOIN employee e
  ON e.department_id = d.id
GROUP BY
    d.name,
    e.salary
ORDER BY
    d.name,
    e.salary;

-- Решение 2: Оконная функция + DISTINCT
SELECT DISTINCT
    d.name     AS dep_name,
    e.salary,
    COUNT(*) OVER (PARTITION BY e.department_id, e.salary) AS count
FROM department d
JOIN employee e
  ON e.department_id = d.id
ORDER BY
    dep_name,
    salary;
```

{{< /details >}}