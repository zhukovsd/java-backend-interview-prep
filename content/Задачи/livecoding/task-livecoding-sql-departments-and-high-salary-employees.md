#### 32. Отделы с числом сотрудников и сотрудники с окладом выше руководителя

**Условие задачи:**  
📌 Есть таблицы 

```sql 
-- Модель данных
-- Отдел:
CREATE TABLE department (
    id          INTEGER     NOT NULL, -- идентификатор отдела
    name        VARCHAR(128) NOT NULL, -- название отдела
    PRIMARY KEY (id)
);

-- Сотрудник:
CREATE TABLE employee (
    id          INTEGER     NOT NULL, -- идентификатор сотрудника
    department_id INTEGER   NOT NULL, -- идентификатор отдела
    manager_id  INTEGER,              -- идентификатор начальника
    name        VARCHAR(128) NOT NULL, -- имя сотрудника
    salary      DECIMAL     NOT NULL, -- оклад сотрудника
    PRIMARY KEY (id),
    FOREIGN KEY (department_id) REFERENCES department(id),
    FOREIGN KEY (manager_id) REFERENCES employee(id)
);


```

1. Вывести список отделов в алфавитном порядке с количеством их сотрудников.  
2. Вывести список сотрудников, чей оклад больше, чем у их непосредственного руководителя.

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}
💡 Для первой задачи используйте `LEFT JOIN` или `JOIN` между `department` и `employee`, агрегируйте по `department.name`, `COUNT(employee.id)` и сортируйте по имени отдела.  
💡 Для второй задачи выполните самосоединение `employee` как `e` и `manager`, сопоставляя `e.manager_id = manager.id`, и отфильтруйте по условию `e.salary > manager.salary`.  
{{< /details >}}

{{< details "Решение" close >}}
```sql
-- 1. Список отделов и количество сотрудников
SELECT
  d.name         AS department_name,
  COUNT(e.id)    AS employee_count
FROM department d
LEFT JOIN employee e
  ON e.department_id = d.id
GROUP BY d.name
ORDER BY d.name;

-- 2. Сотрудники с окладом выше, чем у непосредственного руководителя
SELECT
  e.name         AS employee_name,
  e.salary       AS employee_salary,
  m.name         AS manager_name,
  m.salary       AS manager_salary
FROM employee e
JOIN employee m
  ON e.manager_id = m.id
WHERE e.salary > m.salary;
```

{{< /details >}}