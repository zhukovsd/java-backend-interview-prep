#### 38. Устранение дублирования записей из-за регистра в email

**Условие задачи:**  
📌 Есть таблица `client_service` с колонками:
- `client_id` — идентификатор клиента
- `employee_email` — почта сотрудника, обслуживающего клиента

Из-за разного регистра в `employee_email` (“User@domain.com” и “user@domain.com”) появляются дубликаты записей. Нужно написать SQL-решение, чтобы устранить задвоение.

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Приведите `employee_email` к одному регистру (например, к нижнему) при выборке или в самой таблице.  
💡 Для выборки без дубликатов после нормализации используйте `SELECT DISTINCT … LOWER(employee_email)`.  
💡 Можно создать **уникальный индекс** по выражению `LOWER(employee_email)`, чтобы в будущем избежать вставки дублей с разным регистром.  
{{< /details >}}

{{< details "Решение" close >}}
```sql
-- 1. Выборка без задвоения по разному регистру:
SELECT DISTINCT
  client_id,
  LOWER(employee_email) AS employee_email_normalized
FROM client_service;

-- 2. Обновление существующих записей в таблице:
UPDATE client_service
SET employee_email = LOWER(employee_email);

-- 3. Создание уникального индекса по нормализованному email:
--    В PostgreSQL можно воспользоваться выражением:
CREATE UNIQUE INDEX ux_client_service_email_ci
  ON client_service (client_id, LOWER(employee_email));
````

✅ **Объяснение решения:**

- **Нормализация регистра** через `LOWER(employee_email)` устраняет различия в регистре.

- **SELECT DISTINCT** после нормализации вернёт по одному сочетанию клиент–почта.

- **Обновление** таблицы и **уникальный индекс** не позволят вновь вставить дубли с разным регистром.  
  {{< /details >}}