#### 4. Выбери профили, у которых больше 10 постов

```sql
-- Таблица profile
CREATE TABLE profile (
    id BIGSERIAL PRIMARY KEY,
    nickname VARCHAR,
    registered_at TIMESTAMP
);

-- Таблица post
CREATE TABLE post (
    id BIGSERIAL PRIMARY KEY,
    owner_id BIGINT REFERENCES profile (id),
    body TEXT,
    inserted_at TIMESTAMP,
    likes_count INT
);

-- Таблица subscription_count
CREATE TABLE subscription_count (
    profile_id BIGINT REFERENCES profile (id) UNIQUE,
    followers_count INT,
    following_count INT
);

```


{{< hint warning >}}
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}
📝 Нужно **подсчитать количество постов** у каждого пользователя и выбрать тех, у кого их больше 10.  
🔗 Для этого **соединяем таблицу `profile` с `post` по `id`**.  
📊 Используем **`GROUP BY` и `HAVING`** для фильтрации.
{{< /details >}}

{{< details "Решение" close >}}

```sql
SELECT p.id, p.nickname
FROM profile p
JOIN post po ON p.id = po.owner_id
GROUP BY p.id, p.nickname
HAVING COUNT(po.id) > 10;
```

📌 **Объяснение:**

- **Соединяем `profile` и `post`** по `id` (соответствует `owner_id` в `post`).
- **Группируем по `p.id, p.nickname`**, чтобы считать посты каждого профиля.
- **Фильтруем `HAVING COUNT(po.id) > 10`**, оставляя только тех, у кого больше 10 постов.

🔥 **Преимущества:**  
✅ Эффективный **подсчет через `COUNT`**  
✅ **Фильтрация на уровне запроса**, а не в коде  
✅ **Простая адаптация** (можно добавить другие условия, например, по дате) 🚀
{{< /details >}}