#### 89. Реализация собственного MyArrayList по аналогии с ArrayList

**Условие задачи:**  
📌 Нужно реализовать свой динамический список `MyArrayList<T>` с методами:  
`add`, `get`, `remove`, `size`, а также внутренней логикой расширения массива.  
Поведение должно быть аналогично `java.util.ArrayList`.

**Код (входные данные):**

```java
public class Main {

    public static void Main(String[] args) {
        MyArrayList<String> list = new MyArrayList();

        list.add("Привет");
        list.add("Мир");
        list.add("Java");

        System.out.println("Элемент по индексу 1: " + list.get(1));
        System.out.println("Размер: " + list.size());

        list.remove(1);

        System.out.println("После удаления:");
        for (int i = 0; i < list.size(); i++) {
            System.out.println(list.get(i));
        }
    }
}
```

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Внутри нужно хранить массив `Object[]`.  
💡 Начальный capacity можно взять 10, как в `ArrayList`.  
💡 При заполнении массива надо делать `grow()` → расширять в 1.5–2 раза.  
💡 При удалении нужно сдвинуть элементы `System.arraycopy`.  
💡 Не забыть проверки на выход за пределы.  
{{< /details >}}

{{< details "Решение" close >}}

```java
import java.util.Arrays;

public class MyArrayList<T> {

    private Object[] data;
    private int size;
    private static final int DEFAULT_CAPACITY = 10;

    public MyArrayList() {
        this.data = new Object[DEFAULT_CAPACITY];
    }

    public int size() {
        return size;
    }

    public void add(T element) {
        ensureCapacity(size + 1);
        data[size++] = element;
    }

    public T get(int index) {
        checkIndex(index);
        return (T) data[index];
    }

    public T remove(int index) {
        checkIndex(index);
        T removed = (T) data[index];

        int elementsAfter = size - index - 1;
        if (elementsAfter > 0) {
            System.arraycopy(data, index + 1, data, index, elementsAfter);
        }

        data[--size] = null; // освобождаем ссылку
        return removed;
    }

    private void ensureCapacity(int minCapacity) {
        if (minCapacity > data.length) {
            int newCapacity = data.length + (data.length >> 1); // *1.5
            if (newCapacity < minCapacity) {
                newCapacity = minCapacity;
            }
            data = Arrays.copyOf(data, newCapacity);
        }
    }

    private void checkIndex(int index) {
        if (index < 0 || index >= size) {
            throw new IndexOutOfBoundsException(
                    "Index " + index + " out of bounds for size " + size);
        }
    }
}
```

**Пример работы кода из Main:**

```
Элемент по индексу 1: Мир
Размер: 3
После удаления:
Привет
Java
```

🧠 **Ключевые моменты реализации:**

- Храним элементы в `Object[]`, а при чтении кастуем к `T`.

- При добавлении проверяем, хватает ли места, и расширяем массив.

- При удалении сдвигаем элементы влево, как в `ArrayList`.

- Поддерживаем корректный `size`.


{{< /details >}}