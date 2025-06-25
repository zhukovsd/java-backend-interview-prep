#### 50. Получение только ID через Spring Data JPA

**Условие задачи:**  
📌 В интерфейсе `HeavyDataRepository` надо реализовать метод, который будет возвращать список только `id` сущностей `HeavyData`, а не объекты целиком.

**Код:**

```java
public interface HeavyDataRepository extends JpaRepository<HeavyData, Integer> {

    List<Integer> getHeavyDataIds(); // нужно получить только ID

    List<HeavyData> findAllByIsActive(Boolean isActive);
}
````

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 В Spring Data JPA можно использовать аннотацию `@Query` с JPQL: `select h.id from HeavyData h`.  
💡 При необходимости использовать нативный SQL — `@Query(value = "SELECT id FROM heavy_data", nativeQuery = true)`.  
💡 Метод должен возвращать `List<Integer>`, совпадая с типом выбранного поля.  
{{< /details >}}

{{< details "Решение" close >}}

```java
public interface HeavyDataRepository extends JpaRepository<HeavyData, Integer> {

    // 1) JPQL-запрос через @Query
    @Query("SELECT h.id FROM HeavyData h")
    List<Integer> getHeavyDataIds();

    // 2) (опционально) нативный SQL
    // @Query(value = "SELECT id FROM heavy_data", nativeQuery = true)
    // List<Integer> getHeavyDataIds();
    
    List<HeavyData> findAllByIsActive(Boolean isActive);
}
```

📌 **Что важно помнить:**  
✅ Аннотация `@Query` позволяет явно задать выборку необходимых полей.  
✅ Возвращаемый тип метода (`List<Integer>`) должен соответствовать типу поля в запросе.  
✅ Для нативных запросов указывайте `nativeQuery = true`.  
{{< /details >}}