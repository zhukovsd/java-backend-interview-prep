#### 16. Написать Singleton


{{< hint warning >}}
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}
🔹 Singleton — это паттерн, гарантирующий, что у класса есть только один экземпляр.  
🔹 Лучший способ в Java — **"Lazy Initialization with Holder"**, так как он **потокобезопасный** и **ленивый**.  
🔹 Можно использовать `enum`, но мы реализуем через класс.
{{< /details >}}

{{< details "Решение" close >}}

```java
public class Singleton {
    private Singleton() {
        // Приватный конструктор, чтобы нельзя было создать через new
    }

    private static class SingletonHolder {
        private static final Singleton INSTANCE = new Singleton();
    }

    public static Singleton getInstance() {
        return SingletonHolder.INSTANCE;
    }

    public void doSomething() {
        System.out.println("Работает Singleton!");
    }

    public static void main(String[] args) {
        Singleton singleton1 = Singleton.getInstance();
        Singleton singleton2 = Singleton.getInstance();

        System.out.println(singleton1 == singleton2); // true
        singleton1.doSomething();
    }
}
```

📌 **Объяснение:**

- **Используется `private static class SingletonHolder`**, которая создается только при вызове `getInstance()`.
- **Ленивая инициализация** — экземпляр создается только при первом вызове.
- **Потокобезопасность** без `synchronized` за счет механизма `class loading` в Java.
- **`singleton1 == singleton2` всегда `true`**, так как создается только один объект.

🔥 **Преимущества:**  
✅ **Ленивая инициализация** — создается при первом вызове.  
✅ **Без `synchronized`** — нет накладных расходов.  
✅ **Потокобезопасность** благодаря механизму загрузки классов. 🚀
{{< /details >}}
