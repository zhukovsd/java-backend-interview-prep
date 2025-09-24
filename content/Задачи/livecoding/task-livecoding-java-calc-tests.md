#### 75. Покрытие тестами метода calc

🔥 Нужно продумать тесты для функции `calc(a, b, operation)` по документации.

```java
/*
 * Выполняет одну из операций "+", "-", "/", "*" для переданных параметров
 * В любой непонятной ситуации, в т.ч. невозможность разделить нацело, бросается exception
 */
public Integer calc(Integer a, Integer b, String operation);
```

---

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Покрывай позитивные кейсы для всех операций: `+`, `-`, `*`, `/`.  
💡 Обработай граничные условия: деление на ноль, остаток от деления ≠ 0.  
💡 Проверь `null`-аргументы.  
💡 Проверь некорректный оператор (например, `"%"`, `null`, пустая строка).  
💡 Тестируй большие числа и переполнение.  
💡 Используй JUnit 5 + AssertJ для удобных ассертов.  
💡 Для исключений — `assertThrows`.  
{{< /details >}}

{{< details "Решение" close >}}

Набор тестов (примерно 10–12 штук):

1. **Сложение**: `calc(2, 3, "+") == 5`.

2. **Вычитание**: `calc(10, 4, "-") == 6`.

3. **Умножение**: `calc(6, 7, "*") == 42`.

4. **Деление нацело**: `calc(8, 2, "/") == 4`.

5. **Деление с остатком**: `calc(7, 2, "/")` → exception.

6. **Деление на ноль**: `calc(5, 0, "/")` → exception.

7. **Неизвестная операция**: `calc(5, 2, "%")` → exception.

8. **Оператор `null`**: → exception.

9. **Аргументы `null`** (`a=null` или `b=null`) → exception.

10. **Граничные значения**: `calc(Integer.MAX_VALUE, 1, "+")` → exception или переполнение (проверка).

11. **Негативные числа**: `calc(-3, -7, "*") == 21`.

12. **Идемпотентность**: повторный вызов с одинаковыми аргументами даёт тот же результат.


Пример JUnit 5 теста:

```java
class CalcServiceTest {

    private final CalcService service = new CalcService();

    @Test
    void shouldAddNumbers() {
        assertThat(service.calc(2, 3, "+")).isEqualTo(5);
    }

    @Test
    void shouldThrowWhenDividingByZero() {
        assertThrows(ArithmeticException.class,
            () -> service.calc(5, 0, "/"));
    }

    @Test
    void shouldThrowOnUnsupportedOperation() {
        assertThrows(IllegalArgumentException.class,
            () -> service.calc(5, 2, "%"));
    }
}
```

- Покрытие позитивных и негативных сценариев.

- Ясные названия тестов.

- Используются `assertThrows` и `assertThat`.


{{< /details >}}