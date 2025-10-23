#### 43. Статистика по отделам

**Условие задачи:**  
📌 Есть таблицы `Employees(EmployeeID, Name, DepartmentID, Salary)` и `Departments(DepartmentID, DepartmentName)`.  
Необходимо для каждого отдела вывести:
- название отдела,
- количество сотрудников в отделе,
- среднюю зарплату (округлённую до 2 знаков),
- максимальную зарплату,
- минимальную зарплату.

Отсортировать результат по количеству сотрудников в отделе по убыванию.

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Используйте `LEFT JOIN`, чтобы включить отделы без сотрудников (если нужно).  
💡 Агрегируйте по `d.DepartmentName` с помощью `COUNT()`, `AVG()`, `MAX()`, `MIN()`.  
💡 Для округления средней зарплаты примените функцию `ROUND(..., 2)`.  
💡 Сортируйте по нужной колонке через `ORDER BY employee_count DESC`.  
{{< /details >}}

{{< details "Решение" close >}}
```sql
SELECT
    d.DepartmentName    AS department_name,
    COUNT(e.EmployeeID) AS employee_count,
    ROUND(AVG(e.Salary), 2) AS average_salary,
    MAX(e.Salary)       AS max_salary,
    MIN(e.Salary)       AS min_salary
FROM Departments d
LEFT JOIN Employees e
  ON e.DepartmentID = d.DepartmentID
GROUP BY
    d.DepartmentName
ORDER BY
    employee_count DESC;
````

{{< /details >}}