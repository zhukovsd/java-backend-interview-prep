#### 21. Выгрузка всех новостей с комментариями при большом объеме данных


**Условие задачи:**  
📌 **Необходимо получить все записи из таблицы `news` и соответствующие комментарии из таблицы `comments`, даже при большом объеме данных.**  

- Таблица `news` содержит новости (`id` — автоинкремент).  
- Таблица `comments` содержит комментарии (`news_id` ссылается на `news.id`).  
- Допустим, данных **очень много**, важно избежать перегрузки.

{{< hint warning >}}
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}
💡 Используй **постраничную выборку (pagination)**, чтобы обрабатывать данные частями.  
💡 Важно делать **LEFT JOIN**, чтобы не потерять новости без комментариев.  
💡 Можно использовать **потоковую обработку** или **batch-загрузку** на стороне кода.  
💡 В PostgreSQL можно использовать `FETCH NEXT`, в MySQL — `LIMIT` и `OFFSET`.
{{< /details >}}

{{< details "Решение" close >}}

```sql
-- Пример базового запроса с LEFT JOIN
SELECT n.*, c.*
FROM news n
LEFT JOIN comments c ON n.id = c.news_id;
````

✅ **Как сделать выгрузку эффективной при большом объеме данных:**

1. **Использовать постраничную загрузку:**
    

```sql
-- Пример с LIMIT и OFFSET
SELECT n.*, c.*
FROM news n
LEFT JOIN comments c ON n.id = c.news_id
ORDER BY n.id
LIMIT 1000 OFFSET 0; -- на следующей итерации OFFSET будет 1000, затем 2000 и т.д.
```

2. **Использовать потоковую обработку на сервере (например, JDBC или ORM):**
    

```java
PreparedStatement stmt = connection.prepareStatement(
    "SELECT n.*, c.* FROM news n LEFT JOIN comments c ON n.id = c.news_id ORDER BY n.id",
    ResultSet.TYPE_FORWARD_ONLY,
    ResultSet.CONCUR_READ_ONLY
);
stmt.setFetchSize(1000);
ResultSet rs = stmt.executeQuery();
```

3. **Использовать выгрузку по диапазону `id` (например, `WHERE n.id BETWEEN ...`), чтобы избежать OFFSET.**
    

```sql
SELECT n.*, c.*
FROM news n
LEFT JOIN comments c ON n.id = c.news_id
WHERE n.id BETWEEN 10001 AND 11000
ORDER BY n.id;
```

🔥 Таким образом, даже при большом количестве строк можно **гибко управлять производительностью и ресурсами**, не перегружая систему. {{< /details >}}