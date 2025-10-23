#### 40. Второй по величине оклад

**Условие задачи:**  
📌 Есть таблица `Employee(id, email, salary)`. Нужно получить **второй по величине** уникальный `salary`.

```text
We have table Employee with columns: id, email, salary. 
How would you get the second highest salary?
```


{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Первый вариант — найти максимальный `salary` меньше, чем максимальный в таблице, через подзапрос.  
💡 Второй вариант — использовать оконную функцию `DENSE_RANK()` по `salary` в порядке убывания и выбрать ранг = 2.  
💡 Не забудьте учесть повторяющиеся оклады: нужны **уникальные** значения.  
{{< /details >}}

{{< details "Решение" close >}}
```sql
-- Вариант 1: Подзапрос
SELECT MAX(salary) AS second_highest_salary
FROM Employee
WHERE salary < (
    SELECT MAX(salary) 
    FROM Employee
);

-- Вариант 2: Оконная функция
SELECT salary AS second_highest_salary
FROM (
    SELECT
        salary,
        DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM Employee
) t
WHERE rnk = 2;
````

{{< /details >}}