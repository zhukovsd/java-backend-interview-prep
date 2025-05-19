#### 41. Реализация стека с поддержкой получения минимума за O(1)

**Условие задачи:**  
🔥 Написать класс `MinStack` с методами:  
- `void push(int x)` — добавить элемент в стек;  
- `int pop()` — удалить и вернуть верхний элемент стека;  
- `int top()` — вернуть верхний элемент без удаления;  
- `int peekMin()` — вернуть минимальный элемент в стеке за O(1).

**Код:**

```java
public class MinStack {
    // TODO
}
```

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Используйте **два стека**: один для хранения всех элементов, другой — для хранения текущих минимумов.  
💡 При `push(x)` добавляйте в стек-min либо `x`, если стек-min пуст, либо `min(x, текущий минимум)`.  
💡 При `pop()` удаляйте элементы из обоих стеков одновременно.  
💡 `peekMin()` просто возвращает верхний элемент из второго стека.  
{{< /details >}}

{{< details "Решение" close >}}

```java
import java.util.Stack;

public class MinStack {
    private final Stack<Integer> stack;
    private final Stack<Integer> minStack;

    public MinStack() {
        stack = new Stack<>();
        minStack = new Stack<>();
    }

    public void push(int x) {
        stack.push(x);
        // Если стек мин пуст или новый элемент меньше текущего минимума — пушим его
        if (minStack.isEmpty() || x <= minStack.peek()) {
            minStack.push(x);
        } else {
            // Иначе дублируем текущий минимум
            minStack.push(minStack.peek());
        }
    }

    public int pop() {
        if (stack.isEmpty()) {
            throw new RuntimeException("Стек пуст");
        }
        minStack.pop();
        return stack.pop();
    }

    public int top() {
        if (stack.isEmpty()) {
            throw new RuntimeException("Стек пуст");
        }
        return stack.peek();
    }

    public int peekMin() {
        if (minStack.isEmpty()) {
            throw new RuntimeException("Стек пуст");
        }
        return minStack.peek();
    }

    public static void main(String[] args) {
        MinStack ms = new MinStack();
        ms.push(5);
        ms.push(2);
        ms.push(4);
        System.out.println("Мин: " + ms.peekMin()); // 2
        ms.push(1);
        System.out.println("Мин: " + ms.peekMin()); // 1
        ms.pop();
        System.out.println("Мин: " + ms.peekMin()); // 2
        System.out.println("Top: " + ms.top());    // 4
    }
}
```

{{< /details >}}