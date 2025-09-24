#### 80. Code Review: Синхронизация критической секции в двух потоках

🔥 В текущем коде создаётся новый `Object` для синхронизации при каждом вызове `doSome()`, поэтому потоки синхронизируются на **разных** мониторах — критическая секция фактически не защищена. Кроме того, вызов `new Service.doSome()` некорректен по синтаксису, а `main` должен быть `static`.

```java
class Main{
void main(){
    execute(()->new Service.doSome());
    execute(()->new Service.doSome());
}
}

class Service{
    void doSome(){
        Object obj = new Object();
        //code
        synchronized(obj){
            //code
        }
        //code
    }
}
```

---

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Не создавай монитор внутри метода — он будет разным для каждого вызова. Держи общий `lock` в поле экземпляра (или используй `synchronized` на `this`).  
💡 Запускай оба потока на **одном и том же** экземпляре `Service`, иначе будут разные мониторы.  
💡 Исправь сигнатуру `main` на `public static void main(String[] args)` и вызовы лямбд (`service::doSome`).  
{{< /details >}}

{{< details "Решение (альтернатива с synchronized-методом)" close >}}

```java
class Main {
    public static void main(String[] args) {
        Service service = new Service();
        execute(service::doSome);
        execute(service::doSome);
    }

    static void execute(Runnable r) {
        new Thread(r).start();
    }
}

class Service {
    void doSome() {
        // code
        critical(); // критическая секция вызовом синхронизированного метода
        // code
    }

    private synchronized void critical() {
        // code (критическая секция)
    }
}
```

- Синхронизация на `this` через `synchronized`-метод обеспечивает взаимное исключение между потоками на одном экземпляре.  
  {{< /details >}}