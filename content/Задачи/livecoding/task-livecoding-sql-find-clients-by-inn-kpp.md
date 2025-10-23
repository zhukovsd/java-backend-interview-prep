#### 16. Поиск клиентов в базе данных по INN и KPP


**Условие задачи:**  
📌 **Написать SQL-запрос для поиска всех клиентов по `inn` и `kpp` в таблице `KF_ADAPTER_R_CLIENT`.**

- Используется **простая выборка** по одной таблице.
- В таблице `KF_ADAPTER_R_CLIENT` есть информация о клиентах, включая `inn` (ИНН) и `kpp` (КПП).


```sql
CREATE TABLE KF_ADAPTER_R_CLIENT (
    id SERIAL PRIMARY KEY,
    abs_id VARCHAR(255),
    branch_id VARCHAR(255),
    abs_branch_id VARCHAR(255),
    short_name VARCHAR(255),
    inn VARCHAR(12),
    kpp VARCHAR(9),
    sign_count INT,
    deleted BOOLEAN
);

CREATE TABLE KF_ADAPTER_R_DELEGATE (
    id SERIAL PRIMARY KEY,
    client_id INT,
    abs_id VARCHAR(255),
    user_id VARCHAR(255),
    full_name VARCHAR(255),
    phone VARCHAR(15),
    sign_role VARCHAR(50),
    deleted BOOLEAN,
    FOREIGN KEY (client_id) REFERENCES KF_ADAPTER_R_CLIENT(id)
);

```


{{< hint warning >}}
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}
💡 Для поиска клиентов используем `WHERE`.  
💡 `inn` и `kpp` могут иметь разные значения, поэтому проверяем их **совместно**.  
💡 Если нужно искать клиентов **с определенными значениями**, используем `=`.  
💡 Если необходимо найти клиентов с **похожими значениями**, используем `LIKE`.
{{< /details >}}

{{< details "Решение" close >}}

```sql
SELECT * 
FROM KF_ADAPTER_R_CLIENT 
WHERE inn = '7701234567' 
AND kpp = '770101001';
```

✅ **Объяснение решения:**

- Запрос выбирает **все колонки** (`*`) из таблицы `KF_ADAPTER_R_CLIENT`.
- Фильтрация выполняется по точному совпадению **ИНН** и **КПП** (`WHERE inn = ... AND kpp = ...`).
- Если **нужен поиск по частичному совпадению**, можно использовать `LIKE`:

```sql
SELECT * 
FROM KF_ADAPTER_R_CLIENT 
WHERE inn LIKE '770%' 
AND kpp LIKE '7701%';
```

🔥 Теперь можно находить клиентов по `inn` и `kpp` быстро и просто! 🚀
{{< /details >}}