#### 23. Названия и цены услуг, проданных с 1 января 2021 по следующую неделю

**Условие задачи:**  
📌 Есть таблицы:  
- **Services** (`id`, `name`, `cost`, `price`, `group_id`)  
- **Groups** (`id`, `name`)  
- **Orders** (`id`, `datetime`, `srv_id`)  

Необходимо вывести **название** и **розничную цену** (`price`) для всех услуг, которые продавались **1 января 2021 и в течение следующей недели**.

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Соедините **Orders** с **Services** по `srv_id = Services.id`.  
💡 Отфильтруйте по диапазону дат: от `'2021-01-01'` до `'2021-01-08'` (включительно).  
💡 Используйте `DISTINCT`, чтобы каждая услуга выводилась единожды, даже если была продана несколько раз.  
💡 Альтернативный вариант — сначала выбрать `srv_id` по фильтру дат, а затем присоединить **Services** через `IN` или `EXISTS`.  
{{< /details >}}

{{< details "Решение" close >}}

```sql
-- Решение 1: JOIN + DISTINCT + диапазон дат
SELECT DISTINCT s.name,
       s.price
FROM Services s
JOIN Orders o ON o.srv_id = s.id
WHERE o.datetime >= '2021-01-01'
  AND o.datetime <  '2021-01-09';
```

```sql
-- Решение 2: Использование подзапроса с IN
SELECT s.name,
       s.price
FROM Services s
WHERE s.id IN (
    SELECT o.srv_id
    FROM Orders o
    WHERE o.datetime BETWEEN '2021-01-01' AND '2021-01-08'
);
```

{{< /details >}}