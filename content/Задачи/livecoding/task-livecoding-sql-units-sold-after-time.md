#### 1. Найди id юнитов, которые были проданы на сумму более 1000 рублей после 10:00 сегодняшнего дня. > "2024-06-03 10:00"


```sql
-- Создание таблицы Unit
CREATE TABLE Unit (
    Id INT PRIMARY KEY,
    Price DECIMAL(10, 2)
);

-- Создание таблицы Sales
CREATE TABLE Sales (
    Id INT PRIMARY KEY,
    Unit_id INT,
    Sale_time TIMESTAMP,
    FOREIGN KEY (Unit_id) REFERENCES Unit(Id)
);
```

{{< hint warning >}}
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}
- Нужно выбрать `Unit.Id`, у которых сумма продаж превышает `1000` рублей.
- Для фильтрации использовать `SUM(Price) > 1000` и `Sale_time > '2024-06-03 10:00'`.
- Объединяем таблицы через `JOIN` по `Unit.Id = Sales.Unit_id`.
- Используем `GROUP BY Unit_id`, чтобы агрегировать суммы.
{{< /details >}}

{{< details "Решение" close >}}

```sql
SELECT u.Id 
FROM Unit u
JOIN Sales s ON u.Id = s.Unit_id
WHERE s.Sale_time > '2024-06-03 10:00'
GROUP BY u.Id
HAVING SUM(u.Price) > 1000;
```

Этот SQL-запрос выбирает идентификаторы юнитов (`Id`), у которых сумма продаж после 10:00 превышает 1000 рублей. ✅
{{< /details >}}