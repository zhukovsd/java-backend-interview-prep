#### 46. Проектирование таблиц «Продукты» и «Наличие товаров по магазинам»

**Условие задачи:**  
📌 Необходимо спроектировать структуру таблиц для сети магазинов, чтобы обеспечить:
1. Какие товары есть в том или ином магазине.
2. В каких магазинах есть тот или иной товар.
3. Общее количество каждого товара по всем магазинам.
4. Информацию об отсутствующих товарах в конкретном магазине.

**Код:**

```sql
-- Основная таблица магазинов
CREATE TABLE store (
    id   BIGSERIAL PRIMARY KEY,
    name TEXT       NOT NULL
);

-- Таблица товаров
CREATE TABLE product (
    id          BIGSERIAL PRIMARY KEY,
    name        TEXT       NOT NULL,
    sku         TEXT       UNIQUE NOT NULL,  -- артикул
    description TEXT
);

-- Промежуточная таблица наличия товара в магазине
CREATE TABLE store_inventory (
    store_id   BIGINT NOT NULL
        REFERENCES store(id),
    product_id BIGINT NOT NULL
        REFERENCES product(id),
    quantity   INT    NOT NULL CHECK (quantity >= 0),
    PRIMARY KEY (store_id, product_id)
);
````

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Для связи «многие‑ко‑многим» между магазинами и товарами необходима **junction‑таблица** `store_inventory`.  
💡 Поле `quantity` хранит текущее количество единиц товара в каждом магазине (может быть 0).  
💡 Уникальный ключ `(store_id, product_id)` гарантирует отсутствие дублирующих записей.  
💡 Таблица `product` содержит `sku` (артикул) для внешних интеграций и поиск.  
{{< /details >}}

{{< details "Решение" close >}}

```sql
-- Пример наполнения:
INSERT INTO store (name) VALUES ('На опушке'), ('Речной');

INSERT INTO product (name, sku, description)
VALUES
  ('Хлеб', 'BREAD-001', 'Батон белый'),
  ('Молоко', 'MILK-001', '2.5% жирности'),
  ('Яблоки', 'APPLE-001', 'Зеленые');

-- Указываем наличие товаров в магазинах:
INSERT INTO store_inventory (store_id, product_id, quantity)
VALUES
  (1, 1, 50),  -- в магазине 1 — 50 штук Хлеба
  (1, 2, 30),  -- в магазине 1 — 30 штук Молока
  (2, 2,  0),  -- в магазине 2 — нет Молока
  (2, 3, 20);  -- в магазине 2 — 20 штук Яблок

-- 1) Товары в конкретном магазине:
SELECT p.name, si.quantity
FROM store_inventory si
JOIN product p ON p.id = si.product_id
WHERE si.store_id = 1;

-- 2) Магазины, где есть конкретный товар:
SELECT s.name, si.quantity
FROM store_inventory si
JOIN store s ON s.id = si.store_id
WHERE si.product_id = 2 AND si.quantity > 0;

-- 3) Общее количество товара по всем магазинам:
SELECT p.name,
       SUM(si.quantity) AS total_quantity
FROM product p
LEFT JOIN store_inventory si ON si.product_id = p.id
GROUP BY p.name;

-- 4) Список магазинов, где товар отсутствует:
SELECT s.name
FROM store s
LEFT JOIN store_inventory si
  ON si.store_id = s.id AND si.product_id = 2
WHERE COALESCE(si.quantity, 0) = 0;
```

{{< /details >}}