#### 64. Отделы по алфавиту с количеством сотрудников, где средняя зарплата > 90

**Условие задачи:**  
📌 Нужно вывести список отделов **в алфавитном порядке** с:

- названием отдела

- количеством сотрудников в отделе


И отобрать только те отделы, где **средняя зарплата сотрудников > 90**.

**Код:**

```sql
-- Отдел:
CREATE TABLE department (
    id      INTEGER       NOT NULL,
    name    VARCHAR(128)  NOT NULL,
    PRIMARY KEY (id)
);

-- Сотрудник:
CREATE TABLE employee (
    id             INTEGER       NOT NULL,
    department_id  INTEGER       NOT NULL,
    manager_id     INTEGER,
    name           VARCHAR(128)  NOT NULL,
    salary         DECIMAL       NOT NULL,
    PRIMARY KEY (id),
    FOREIGN KEY (department_id) REFERENCES department(id),
    FOREIGN KEY (manager_id) REFERENCES employee(id)
);
```

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Соединяем `department` и `employee` по `department_id`.  
💡 Группируем по отделу (`GROUP BY d.id, d.name`).  
💡 `COUNT(e.id)` — количество сотрудников.  
💡 `HAVING AVG(e.salary) > 90` — фильтр по средней зарплате.  
💡 `ORDER BY d.name` — алфавитный порядок.  
{{< /details >}}

{{< details "Решение" close >}}

```sql
SELECT
    d.name AS department_name,
    COUNT(e.id) AS employees_count
FROM department d
JOIN employee e
  ON e.department_id = d.id
GROUP BY d.id, d.name
HAVING AVG(e.salary) > 90
ORDER BY d.name;
```

{{< /details >}}