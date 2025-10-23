#### 36. Папки с AVI-файлами и пустые папки


**Условие задачи:**  
📌 Написать SQL-запрос, который выбрал бы **названия папок**, в которых есть хотя бы один файл с расширением `.avi`, **или** которые являются **пустыми** (не содержат ни одного файла).

**Структура таблиц:**

```sql
CREATE TABLE folder (
  id uuid PRIMARY KEY,
  name text NOT NULL
);

CREATE TABLE file (
  id uuid PRIMARY KEY,
  name text NOT NULL,
  folder_id uuid NOT NULL,
  CONSTRAINT fk__file__folder_id FOREIGN KEY (folder_id) REFERENCES folder (id)
);

```

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Используем `LEFT JOIN` — чтобы получить и те папки, в которых **нет файлов вообще**.  
💡 Для файлов с `.avi` используем фильтр `file.name LIKE '%.avi'`.  
💡 Для определения пустых папок проверяем `file.id IS NULL`.  
💡 Не забудьте использовать `DISTINCT`, если JOIN может дать дубликаты.  
{{< /details >}}

{{< details "Решение" close >}}
```sql
SELECT DISTINCT f.name
FROM folder f
LEFT JOIN file fi ON f.id = fi.folder_id
WHERE fi.name LIKE '%.avi'
   OR fi.id IS NULL;
```

✅ **Объяснение решения:**

- `LEFT JOIN` соединяет папки с файлами (если они есть), оставляя строки для пустых папок.
    
- `fi.name LIKE '%.avi'` — выбираем папки с AVI-файлами.
    
- `fi.id IS NULL` — означает, что в папке нет ни одного файла.
    
- `DISTINCT` убирает возможные дубликаты, если в папке несколько AVI-файлов.
    

🔥 Один запрос — и у нас и видео, и пустота 😎  
{{< /details >}}