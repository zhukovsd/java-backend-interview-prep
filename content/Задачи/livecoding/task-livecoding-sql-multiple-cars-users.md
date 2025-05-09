#### 2. Написать SQL-запрос, чтобы вывести пользователей, у которых более одного автомобиля?


```sql
CREATE TABLE USER (
    id INT,
    name VARCHAR(50)
);

INSERT INTO USER (id, name) VALUES
(1, 'Ivan'),
(2, 'Oleg'),
(3, 'Anna'),
(4, 'Ivan'),
(5, 'Ted');

CREATE TABLE CAR (
    id INT,
    model VARCHAR(50)
);

INSERT INTO CAR (id, model) VALUES
(4422, 'Opel 1'),
(4523, 'BMV 5'),
(4612, 'VW'),
(4853, 'BMV 6');

```

{{< hint warning >}}
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}
- Нам нужно **посчитать количество машин** у каждого пользователя.
- Используем `GROUP BY user_id` для группировки по пользователям.
- Применяем `HAVING COUNT(*) > 1`, чтобы оставить только тех, у кого **больше одной машины**.
- Убедимся, что таблица `CAR` связана с `USER`, добавив `user_id` в `CAR`.
{{< /details >}}

{{< details "Решение" close >}}

Добавляем внешний ключ, если его нет:

```sql
ALTER TABLE CAR ADD COLUMN user_id INT;
ALTER TABLE CAR ADD FOREIGN KEY (user_id) REFERENCES USER(id);
```

Запрос для поиска пользователей с более чем одной машиной:

```sql
SELECT u.id, u.name, COUNT(c.id) AS car_count
FROM USER u
JOIN CAR c ON u.id = c.user_id
GROUP BY u.id, u.name
HAVING COUNT(c.id) > 1;
```

🔹 **Как это работает?**

- `JOIN` объединяет таблицы `USER` и `CAR` по `user_id`.
- `GROUP BY u.id, u.name` группирует данные по пользователям.
- `COUNT(c.id)` считает количество машин у каждого пользователя.
- `HAVING COUNT(c.id) > 1` оставляет только пользователей с **двумя и более машинами**.

**Выход:**

```
id | name  | car_count
----------------------
1  | Ivan  | 2
```

Значит, **у пользователя "Ivan" больше одной машины**. 🚗🚗
{{< /details >}}