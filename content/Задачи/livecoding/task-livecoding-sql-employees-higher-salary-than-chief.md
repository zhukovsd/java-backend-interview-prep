#### 55. Сотрудники, получающие зарплату выше своего руководителя

**Условие задачи:**  
📌 В таблице `EMPLOYEE(ID, NAME, SALARY, DEPARTMENT_ID, CHIEF_ID)` нужно вывести **список сотрудников**, чья зарплата больше, чем у их **непосредственного руководителя** (`CHIEF_ID`).

**Код:**

```sql
CREATE TABLE EMPLOYEE (
    ID int,
    NAME VARCHAR(256),
    SALARY int,
    DEPARTMENT_ID int,
    CHIEF_ID int
);

-- Пример данных
-- ID | NAME      | SALARY | DEPARTMENT_ID | CHIEF_ID
-- ---+------------+--------+---------------+----------
-- 1  | Иванов     | 150    | 1             | null
-- 2  | Петров     | 250    | 1             | 1
-- 3  | Сидоров    | 550    | 1             | 1
-- 4  | Медведев   | 350    | 2             | 6
-- 5  | Соколов    | 350    | 2             | 6
-- 6  | Рыбников   | 150    | 2             | null
-- 7  | Арбузов    | 450    | 3             | null
-- 8  | Орехов     | 650    | 3             | 7
-- 9  | Дынин      | 50     | 3             | 7
```

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Используйте **self join** — соедините таблицу `EMPLOYEE` саму с собой.  
💡 Условие соединения: `e.CHIEF_ID = c.ID`, где `c` — начальник.  
💡 В `WHERE` оставьте только тех, у кого `e.SALARY > c.SALARY`.  
💡 Исключите записи, где `CHIEF_ID IS NULL` — у них нет руководителя.  
{{< /details >}}

{{< details "Решение" close >}}

```sql
SELECT 
    e.NAME       AS employee_name,
    e.SALARY     AS employee_salary,
    c.NAME       AS chief_name,
    c.SALARY     AS chief_salary
FROM EMPLOYEE e
JOIN EMPLOYEE c
  ON e.CHIEF_ID = c.ID
WHERE e.SALARY > c.SALARY;
```

**Результат для примера данных:**

|employee_name|employee_salary|chief_name|chief_salary|
|---|---|---|---|
|Петров|250|Иванов|150|
|Сидоров|550|Иванов|150|
|Медведев|350|Рыбников|150|
|Соколов|350|Рыбников|150|
|Орехов|650|Арбузов|450|

🧠 **Пояснение:**

- Таблица соединяется сама с собой, чтобы “достать” зарплату начальника.

- Сравнение выполняется построчно: если `SALARY > CHIEF.SALARY`, строка попадает в результат.


{{< /details >}}