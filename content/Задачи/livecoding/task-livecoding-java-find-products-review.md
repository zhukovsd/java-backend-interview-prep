#### 79. Code Review: Метод find в сервисе интернет-магазина

🔥 Пользователь заходит на страничку магазина, выбирает фильтры для отображения товаров. Фильтров больше 5. Когда пользователь выбрал фильтры и нажал Найти, к нам на бэк поступает запрос, и прилетает в этот участок кода, в метод find(Filter filter). Мы ищем кол-во товаров, которые удовлетворяют фильтру. Если их больше 0, то загружаем товары, иначе возвращаем пустой список. Этот метод вызывается 200 раз в секунду. В 50 процентах случаев count = 0

```java
class Service {
    //200 RPS
    //50% count = 0
    
    public List<Product> find(Filter filter) {
        int count = repo.count(filter);
        if(count > 0) {
            return repo.find(filter);
        }
        return new ArrayList<>();
    }
}
```

---

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Двойной запрос к БД (`count` + `find`) нагружает систему. Лучше сделать один.  
💡 Если нужно вернуть список, просто вызывай `repo.find(filter)` и проверяй результат на пустоту.  
💡 При RPS=200 и 50% пустых результатов нагрузка на БД возрастает ×2.  
💡 Можно добавить кэширование фильтров с нулевым результатом (например, на 30 секунд).  
💡 Используй `Collections.emptyList()` вместо `new ArrayList<>()`.  
💡 Для часто повторяющихся фильтров рассмотри in-memory кэш или Redis.  
💡 Если фильтров много и сложные запросы, подумай о денормализации данных или использовании search-движка (Elastic, Solr).  
{{< /details >}}

{{< details "Решение" close >}}

Исправленный вариант:

```java
class Service {
    public List<Product> find(Filter filter) {
        List<Product> products = repo.find(filter);
        return products.isEmpty() ? Collections.emptyList() : products;
    }
}
```

Вариант с кэшированием пустых результатов:

```java
class Service {
    private final Cache<Filter, Boolean> emptyResultCache = Caffeine.newBuilder()
            .expireAfterWrite(Duration.ofSeconds(30))
            .build();

    public List<Product> find(Filter filter) {
        if (Boolean.TRUE.equals(emptyResultCache.getIfPresent(filter))) {
            return Collections.emptyList();
        }

        List<Product> products = repo.find(filter);

        if (products.isEmpty()) {
            emptyResultCache.put(filter, true);
            return Collections.emptyList();
        }

        return products;
    }
}
```

- Убран лишний `count`-запрос.
- Возврат оптимизирован с использованием `Collections.emptyList()`.
- Возможность кешировать частые фильтры с пустыми результатами.


{{< /details >}}