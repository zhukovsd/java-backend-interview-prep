#### 12. Покажите название компании, название отдела и количество сотрудников в отделе

**Условие задачи:**  
🔍 **Написать SQL-запрос, который покажет название компании, название отдела и количество сотрудников в этом отделе.**

```sql
CREATE table if not exists company (
    id uuid primary key,
    name_ varchar NOT NULL
);

CREATE table if not exists department (
    id uuid primary key,
    name_ varchar NOT NULL,
    company_id uuid NOT NULL,
    CONSTRAINT department_fk FOREIGN KEY (company_id) REFERENCES company(id)
);

CREATE table if not exists employee (
    id uuid primary key,
    name_ varchar NOT NULL,
    department_id uuid NOT NULL,
    CONSTRAINT employee_fk FOREIGN KEY (department_id) REFERENCES department(id)
);

insert into company (id, name_) values(1, 'Company 1') ON CONFLICT DO NOTHING;
insert into company (id, name_) values(2, 'Company 2') ON CONFLICT DO NOTHING;
insert into company (id, name_) values(3, 'Company 3') ON CONFLICT DO NOTHING;

insert into department (id, name_, company_id) values(1, 'Department 1', 1) ON CONFLICT DO NOTHING;
insert into department (id, name_, company_id) values(2, 'Department 2', 1) ON CONFLICT DO NOTHING;
insert into department (id, name_, company_id) values(3, 'Department 3', 2) ON CONFLICT DO NOTHING;

insert into employee (id, name_, department_id) values(1, 'Employee 1', 1) ON CONFLICT DO NOTHING;
insert into employee (id, name_, department_id) values(2, 'Employee 2', 2) ON CONFLICT DO NOTHING;
insert into employee (id, name_, department_id) values(3, 'Employee 3', 3) ON CONFLICT DO NOTHING;

```


{{< hint warning >}}
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}
💡 Нужно использовать `JOIN` для соединения таблиц `company`, `department` и `employee`.  
💡 Используйте `COUNT(*)` для подсчета количества сотрудников в каждом отделе.  
💡 Группировка `GROUP BY` поможет сгруппировать результаты по компании и отделу.
{{< /details >}}

{{< details "Решение" close >}}

```sql
SELECT 
    c.name_ AS company_name,
    d.name_ AS department_name,
    COUNT(e.id) AS employee_count
FROM company c
JOIN department d ON c.id = d.company_id
LEFT JOIN employee e ON d.id = e.department_id
GROUP BY c.name_, d.name_
ORDER BY c.name_, d.name_;
```

✅ **Объяснение решения:**

- Соединяем `company` и `department` по `company_id`.
- Используем `LEFT JOIN`, чтобы учесть отделы без сотрудников.
- `COUNT(e.id)` считает количество сотрудников в каждом отделе.
- `GROUP BY` группирует данные по названию компании и отдела.
- `ORDER BY` сортирует результат по компании и отделу.

🚀 **Теперь запрос выдаст список всех компаний, их отделов и количество сотрудников в каждом отделе!** 🔥
{{< /details >}}