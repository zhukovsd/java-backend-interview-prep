#### 47. Сумма выручки по продавцам

🔥 Напишите SQL-запрос, который извлекает идентификаторы всех продавцов и общую сумму, заработанную каждым. В выборке должны быть и продавцы без заказов (с суммой 0). Результат отсортирован по id продавца.

```sql
CREATE TABLE Salespersons
(
        id SERIAL PRIMARY KEY,
        name VARCHAR(20)
);

CREATE TABLE Customers
(
        id SERIAL PRIMARY KEY,
        salesperson_id INT
);

CREATE TABLE Orders
(
        id SERIAL PRIMARY KEY,
        customer_id INT,
        price INT
);

TRUNCATE Orders;
INSERT INTO Orders (customer_id, price)
VALUES (8, 2480),
        (6, 654),
        (13, 2938),
        ... (больше строк)
        (2, 1548);

INSERT INTO Salespersons (name)
VALUES ('Stephanie'),
        ('Rebecca'),
        ('Deborah'),
        ...
        ('Maria');

INSERT INTO Customers (salesperson_id)
VALUES (5),
        (4),
        (8),
        ...
        (3);
```

---

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Начинай запрос от таблицы `Salespersons`, чтобы гарантировать наличие всех продавцов.  
💡 Используй `LEFT JOIN` с `Customers` и `Orders`, чтобы не потерять продавцов без продаж.  
💡 Для суммирования выручки применяй `SUM(o.price)`.  
💡 Чтобы у продавцов без заказов не было `NULL`, используй `COALESCE(..., 0)`.  
💡 Группировка должна быть по `s.id`, сортировка — `ORDER BY s.id`.  
{{< /details >}}

{{< details "Решение" close >}}

```sql
SELECT
    s.id AS salesperson_id,
    COALESCE(SUM(o.price), 0) AS total_earned
FROM Salespersons s
LEFT JOIN Customers c
       ON c.salesperson_id = s.id
LEFT JOIN Orders o
       ON o.customer_id = c.id
GROUP BY s.id
ORDER BY s.id;
```

{{< /details >}}