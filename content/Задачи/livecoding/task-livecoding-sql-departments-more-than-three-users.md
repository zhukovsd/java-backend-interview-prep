#### 65. Департаменты с количеством сотрудников больше трёх

**Условие задачи:**  
📌 Есть таблицы `department` и `users`.  
Нужно вывести **департаменты**, в которых **количество сотрудников больше 3**.

**Схема данных:**

```sql
CREATE TABLE department (
    department_id BIGINT PRIMARY KEY,
    department_name VARCHAR(255)
);

CREATE TABLE users (
    user_id BIGINT PRIMARY KEY,
    full_name VARCHAR(255),
    department_id BIGINT,
    CONSTRAINT fk_department
        FOREIGN KEY (department_id)
        REFERENCES department(department_id)
);
```

---

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Нужно посчитать количество сотрудников по каждому департаменту.  
💡 Используем `GROUP BY department_id`.  
💡 Фильтрация по количеству выполняется через `HAVING`, а не `WHERE`.  
💡 Чтобы вывести название департамента — делаем `JOIN`.  
{{< /details >}}

---

{{< details "Решение" close >}}

```sql
SELECT
    d.department_id,
    d.department_name,
    COUNT(u.user_id) AS user_count
FROM department d
JOIN users u
  ON u.department_id = d.department_id
GROUP BY d.department_id, d.department_name
HAVING COUNT(u.user_id) > 3;
```

{{< /details >}}