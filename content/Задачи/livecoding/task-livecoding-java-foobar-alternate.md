#### 55. Чередование вывода `foo` и `bar`

**Условие задачи:**  
📌 Дана класс `FooBar`, в котором методы `foo()` и `bar()` выводят строки `"foo"` и `"bar"` по `n` раз каждый. Одна и та же инстанция передаётся двум потокам:
- Поток A вызывает `foo()`
- Поток B вызывает `bar()`

Необходимо изменить реализацию так, чтобы вывод был чередованием `"foobarfoobar..."` ровно `n` раз.

**Код:**

```java
import java.util.concurrent.Semaphore;

class FooBar {
    private final int n;
    // TODO: добавить синхронизацию

    public FooBar(int n) {
        this.n = n;
        // TODO: инициализация
    }

    public void foo() throws InterruptedException {
        for (int i = 0; i < n; i++) {
            // TODO: ожидание и вывод "foo"
            System.out.print("foo");
            // TODO: разрешить bar()
        }
    }

    public void bar() throws InterruptedException {
        for (int i = 0; i < n; i++) {
            // TODO: ожидание и вывод "bar"
            System.out.print("bar");
            // TODO: разрешить foo()
        }
    }
}
````

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Используйте два `Semaphore`:

- `semFoo` с начальными разрешениями = 1,

- `semBar` с начальными разрешениями = 0.  
  💡 В методе `foo()` перед выводом вызывайте `semFoo.acquire()`, после — `semBar.release()`.  
  💡 В методе `bar()` перед выводом вызывайте `semBar.acquire()`, после — `semFoo.release()`.  
  💡 Это гарантирует строгую чередующуюся последовательность.  
  {{< /details >}}


{{< details "Решение" close >}}

```java
import java.util.concurrent.Semaphore;

class FooBar {
    private final int n;
    private final Semaphore semFoo = new Semaphore(1);
    private final Semaphore semBar = new Semaphore(0);

    public FooBar(int n) {
        this.n = n;
    }

    public void foo() throws InterruptedException {
        for (int i = 0; i < n; i++) {
            semFoo.acquire();        // ждём разрешения для foo
            System.out.print("foo");
            semBar.release();        // разрешаем bar
        }
    }

    public void bar() throws InterruptedException {
        for (int i = 0; i < n; i++) {
            semBar.acquire();        // ждём разрешения для bar
            System.out.print("bar");
            semFoo.release();        // разрешаем foo
        }
    }

    // Тестирование с двумя потоками
    public static void main(String[] args) {
        int n = 5;
        FooBar fb = new FooBar(n);

        Thread t1 = new Thread(() -> {
            try {
                fb.foo();
            } catch (InterruptedException ignored) {}
        });
        Thread t2 = new Thread(() -> {
            try {
                fb.bar();
            } catch (InterruptedException ignored) {}
        });

        t1.start();
        t2.start();
    }
}
```

{{< /details >}}