#### 104. Реализация стека с `push`, `pop`, `peekMax` за O(1)

**Условие задачи:**  
📌 Нужно реализовать свой класс **стека**, который поддерживает операции:

- `push(x)` — положить элемент в стек

- `pop()` — удалить верхний элемент

- `peekMax()` — вернуть **максимальный элемент в стеке за O(1)**


**Код:**

```java
// Реализовать стек с поддержкой max за O(1)
```

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Классическое решение — использовать **два стека**.  
💡 Основной стек хранит значения.  
💡 Второй стек хранит текущий максимум на каждом уровне.  
💡 Тогда `peekMax()` работает за O(1).  
{{< /details >}}

{{< details "Решение" close >}}

```java
import java.util.ArrayDeque;
import java.util.Deque;
import java.util.NoSuchElementException;

public class MaxStack {

    private final Deque<Integer> stack = new ArrayDeque<>();
    private final Deque<Integer> maxStack = new ArrayDeque<>();

    public void push(int value) {
        stack.push(value);

        if (maxStack.isEmpty()) {
            maxStack.push(value);
        } else {
            maxStack.push(Math.max(value, maxStack.peek()));
        }
    }

    public int pop() {
        if (stack.isEmpty()) {
            throw new NoSuchElementException("Stack is empty");
        }
        maxStack.pop();
        return stack.pop();
    }

    public int peekMax() {
        if (maxStack.isEmpty()) {
            throw new NoSuchElementException("Stack is empty");
        }
        return maxStack.peek();
    }
}
```

{{< /details >}}