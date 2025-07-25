#### 47. Оптимизация медленного SQL‑запроса

**Условие задачи:**  
📌 Есть таблицы `customers` и `orders`:
```sql
CREATE TABLE customers
(
    customer_id        NUMERIC(15) PRIMARY KEY,
    name               VARCHAR(100),
    email              VARCHAR(100) UNIQUE,
    registration_date  TIMESTAMP,
    premium_member     BOOLEAN
);

CREATE TABLE orders
(
    order_id       NUMERIC(15) PRIMARY KEY,
    customer_id    NUMERIC(15) REFERENCES customers(customer_id),
    order_date     TIMESTAMP,
    total_amount   DECIMAL(12, 2),
    status         VARCHAR(20)
);
````

Медленно отрабатывает запрос:

```sql
SELECT c.name,
       c.email,
       o.order_id,
       o.order_date,
       o.total_amount
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
WHERE o.status = 'Processing'
  AND c.premium_member = TRUE
ORDER BY o.order_date DESC;
```

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Определите **бизнес‑логику**: выбираем все заказы со статусом “Processing” только у премиум‑клиентов, сортируя по дате последнего заказа.  
💡 Проверьте план выполнения (`EXPLAIN ANALYZE`) – ищутся ли полные сканы по таблицам?  
💡 Добавьте **композитные индексы** по колонкам, участвующим в `WHERE` и `JOIN`, и включите в них поле для сортировки:

```sql
CREATE INDEX idx_orders_status_date ON orders(status, order_date DESC, customer_id);
CREATE INDEX idx_customers_premium ON customers(premium_member, customer_id);
```

💡 Рассмотрите **покрывающие индексы** или **materialized view** для часто повторяющихся запросов.  
💡 Убедитесь, что статистика таблиц актуальна (`ANALYZE`).  
{{< /details >}}

{{< details "Решение" close >}}

1. **Понять бизнес‑смысл запроса**

    - Мы хотим отобразить все **текущие (Processing)** заказы только у **премиум‑пользователей**, отсортированные от самых свежих.

2. **Изучить план выполнения**

    ```sql
    EXPLAIN ANALYZE
    SELECT …;
    ```

    - Если видим `Seq Scan` по таблице `orders` или `customers` → недостаточно индексов.

3. **Создать индексы**

    - На таблицу `orders`:

        ```sql
        CREATE INDEX idx_orders_status_date
          ON orders(status, order_date DESC, customer_id);
        ```

      Это позволит фильтровать по статусу и сразу выдавать результаты в нужном порядке, а затем давать `customer_id` для `JOIN`.

    - На таблицу `customers`:

        ```sql
        CREATE INDEX idx_customers_premium
          ON customers(premium_member, customer_id);
        ```

      Чтобы быстро выбрать только премиум‑клиентов и затем выполнить `JOIN` по `customer_id`.

4. **Перепроверить план**

    - После создания индексов убедитесь, что `EXPLAIN ANALYZE` показывает `Index Scan` вместо `Seq Scan`.

5. **Дополнительные оптимизации**

    - Если данные редко меняются, можно создать **материализованное представление**:

        ```sql
        CREATE MATERIALIZED VIEW mv_processing_premium AS
        SELECT c.name, c.email, o.order_id, o.order_date, o.total_amount
        FROM customers c
        JOIN orders o ON c.customer_id = o.customer_id
        WHERE o.status = 'Processing'
          AND c.premium_member = TRUE;
        CREATE INDEX ON mv_processing_premium(order_date DESC);
        ```

    - **Регулярно обновлять** материализованное представление (`REFRESH MATERIALIZED VIEW`).

6. **Заключение**

    - Правильно подобранные индексы и анализ плана выполнения устраняют полные сканирования таблиц и значительно ускоряют запрос на продакшене.  
      {{< /details >}}