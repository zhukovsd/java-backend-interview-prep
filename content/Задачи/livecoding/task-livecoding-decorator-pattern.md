#### 6. Реализовать паттерн Декоратор в Java?


{{< hint warning >}}
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}
- Паттерн **Декоратор** позволяет динамически добавлять новое поведение объекту, оборачивая его в другой объект.
- Для этого создается базовый интерфейс, который реализуют как декорируемые классы, так и их декораторы.
- Декораторы должны реализовывать тот же интерфейс и делегировать вызовы объекту, который они декорируют, добавляя дополнительные действия до или после делегирования.
- Паттерн полезен, если нужно добавлять функциональность объекту без изменения его кода.
{{< /details >}}

{{< details "Решение" close >}}

Для демонстрации паттерна Декоратор создадим систему, где будем декорировать объект, добавляя разные действия, например, к текстовому сообщению.

 **Шаг 1. Определим интерфейс**

```java
public interface Message {
    String getContent();
}
```

 **Шаг 2. Реализуем базовый класс**

```java
public class SimpleMessage implements Message {

    private String content;

    public SimpleMessage(String content) {
        this.content = content;
    }

    @Override
    public String getContent() {
        return content;
    }
}
```

 **Шаг 3. Создадим декоратор**

```java
public class MessageDecorator implements Message {

    protected Message wrappedMessage;

    public MessageDecorator(Message message) {
        this.wrappedMessage = message;
    }

    @Override
    public String getContent() {
        return wrappedMessage.getContent();
    }
}
```

 **Шаг 4. Реализуем конкретные декораторы**

```java
public class UppercaseDecorator extends MessageDecorator {

    public UppercaseDecorator(Message message) {
        super(message);
    }

    @Override
    public String getContent() {
        return wrappedMessage.getContent().toUpperCase();
    }
}

public class ExclamationDecorator extends MessageDecorator {

    public ExclamationDecorator(Message message) {
        super(message);
    }

    @Override
    public String getContent() {
        return wrappedMessage.getContent() + "!!!";
    }
}
```

 **Шаг 5. Используем декоратор**

```java
public class Main {
    public static void main(String[] args) {
        Message message = new SimpleMessage("Hello");
        
        // Применяем декораторы
        message = new UppercaseDecorator(message);
        message = new ExclamationDecorator(message);

        System.out.println(message.getContent());  // Выведет "HELLO!!!"
    }
}
```

 **Ключевые моменты:**

1. **Интерфейс `Message`** определяет общие методы для всех сообщений.
2. **Класс `SimpleMessage`** - это базовая реализация, которая возвращает сообщение.
3. **`MessageDecorator`** - абстрактный декоратор, который хранит ссылку на объект `Message` и делегирует вызовы.
4. Конкретные декораторы, такие как **`UppercaseDecorator`** и **`ExclamationDecorator`**, расширяют функциональность базового объекта, изменяя или дополняя поведение.

Таким образом, паттерн Декоратор позволяет гибко расширять функциональность объектов, не изменяя их код.
{{< /details >}}

