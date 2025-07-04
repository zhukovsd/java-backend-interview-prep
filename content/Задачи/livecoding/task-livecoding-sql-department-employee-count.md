#### 38. Отделы с количеством сотрудников

**Условие задачи:**  
📌 Есть таблицы:
- **dep** (`id`, `name`) — отделы
- **emp** (`id`, `id_dep`, `fio`) — сотрудники, где `id_dep` ссылается на `dep.id`

Необходимо получить список **всех отделов** с количеством сотрудников в каждом. Если в отделе нет сотрудников, вывести `0`.

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Используйте `LEFT JOIN`, чтобы включить отделы без сотрудников.  
💡 Агрегируйте по `d.name` и примените `COUNT(e.id)`, он вернёт `0` для пустых групп.  
💡 При необходимости сортируйте результат по имени отдела для удобства.  
{{< /details >}}

{{< details "Решение" close >}}
```sql
SELECT
  d.name           AS department,
  COUNT(e.id)      AS employee_count
FROM dep d
LEFT JOIN emp e
  ON e.id_dep = d.id
GROUP BY d.name
ORDER BY d.name;
```

{{< /details >}}