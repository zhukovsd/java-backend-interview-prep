#### 25. Таблицы для авторов и книг (многие-ко-многим)

**Условие задачи:**  
📌 Есть две сущности: **Авторы** и **Книги**.  
Каждый автор может иметь много книг, и каждая книга может иметь много авторов (отношение «многие-ко-многим»).  
Необходимо описать структуру таблиц для реализации такой связи.

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 В классической реляционной модели многие-ко-многим реализуется через **промежуточную (junction) таблицу**.  
💡 Основные таблицы — `authors` и `books` — содержат информацию об авторах и книгах соответственно.  
💡 Junction-таблица хранит пары `(author_id, book_id)`.  
{{< /details >}}

{{< details "Решение" close >}}
```sql
-- Основная таблица авторов
CREATE TABLE authors (
    id   SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL
);

-- Основная таблица книг
CREATE TABLE books (
    id    SERIAL PRIMARY KEY,
    title VARCHAR(255) NOT NULL
);

-- Промежуточная таблица для связи многие-ко-многим
CREATE TABLE author_book (
    author_id INT NOT NULL,
    book_id   INT NOT NULL,
    PRIMARY KEY (author_id, book_id),
    FOREIGN KEY (author_id) REFERENCES authors(id),
    FOREIGN KEY (book_id)   REFERENCES books(id)
);
```

{{< /details >}}