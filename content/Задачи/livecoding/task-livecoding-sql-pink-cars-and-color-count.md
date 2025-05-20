#### 32. Машины розового цвета и количество по цветам

**Условие задачи:**  
📌 Есть таблицы:  

```sql
CREATE TABLE colors (
    id INT NOT NULL PRIMARY KEY,
    name VARCHAR(50),
    UNIQUE(name)
);

CREATE TABLE cars (
    id INT NOT NULL PRIMARY KEY,
    name VARCHAR(50),
    color_id INT,
    FOREIGN KEY (color_id) REFERENCES colors(id)
);
```

1. Вывести список всех машин розового цвета.  
2. Посчитать количество машин для каждого цвета и вывести в формате: название цвета и количество машин.

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Для первой задачи используйте `JOIN` и фильтрацию по `colors.name = 'pink'`.  
💡 Для второй — агрегируйте по `colors.name` с помощью `GROUP BY` и `COUNT(*)`.  
💡 Не забудьте применять `LEFT JOIN`, если нужно включить цвета без машин (опционально).  
{{< /details >}}

{{< details "Решение" close >}}
```sql
-- 1. Список машин розового цвета
SELECT c.name AS car_name
FROM cars c
JOIN colors col ON c.color_id = col.id
WHERE col.name = 'pink';

-- 2. Количество машин по каждому цвету
SELECT col.name    AS color,
       COUNT(c.id) AS car_count
FROM colors col
LEFT JOIN cars c ON c.color_id = col.id
GROUP BY col.name
ORDER BY col.name;
```

{{< /details >}}