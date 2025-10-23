#### 17. Делегаты клиентов: найти по INN/KPP, вывести имя и телефон

**Условие задачи:**  
📌 **Написать SQL-запрос для получения списка делегатов (`KF_ADAPTER_R_DELEGATE`) по `inn` и `kpp` из таблицы `KF_ADAPTER_R_CLIENT`.**

- Необходимо вывести только `full_name` (полное имя) и `phone` (телефон).
- Таблицы связаны по `client_id`.


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
💡 Связь между таблицами идет по **client_id**.  
💡 Для соединения таблиц используем `JOIN`.  
💡 Фильтруем данные по `inn` и `kpp` из таблицы `KF_ADAPTER_R_CLIENT`.  
💡 Если необходимо исключить удаленных (`deleted = TRUE`), добавляем соответствующий фильтр.

{{< /details >}}

{{< details "Решение" close >}}

```sql
SELECT d.full_name, d.phone 
FROM KF_ADAPTER_R_DELEGATE d
JOIN KF_ADAPTER_R_CLIENT c ON d.client_id = c.id
WHERE c.inn = '7701234567' 
AND c.kpp = '770101001'
AND d.deleted = FALSE;
```

✅ **Объяснение решения:**

- **`JOIN`** связывает таблицы `KF_ADAPTER_R_CLIENT` и `KF_ADAPTER_R_DELEGATE` по `client_id`.
- **Фильтр по `inn` и `kpp`** выбирает только клиентов с указанными значениями.
- **`d.deleted = FALSE`** исключает удаленных делегатов.
- **Выбираем только нужные поля**: `full_name` и `phone`.

🔥 Теперь можно получать список делегатов по ИНН и КПП! 🚀
{{< /details >}}