#### 42. Гибкий NotificationService с Spring

**Условие задачи:**  
🔥 Написать сервис `Notificator`, который отправляет сообщения через различные внешние библиотеки (Email, SMS и т.д.), с возможностью легко добавлять новые каналы уведомлений, используя возможности Java и Spring.

**Код:**

```java
// внешняя библиотека 1
public class EmailNotificationService {
    public void sendEmail(String message) {
        System.out.println("Send from email service");
        System.out.println(message);
    }
}

// внешняя библиотека 2
public class SmsNotificationService {
    public void sendSms(String message) {
        System.out.println("Send from sms service");
        System.out.println(message);
    }
}

// Ваш код начинается здесь
public interface NotificationService {
    void send(String message);
}

@Service
public class EmailNotificationAdapter implements NotificationService {
    private final EmailNotificationService emailService;

    public EmailNotificationAdapter(EmailNotificationService emailService) {
        this.emailService = emailService;
    }

    @Override
    public void send(String message) {
        emailService.sendEmail(message);
    }
}

@Service
public class SmsNotificationAdapter implements NotificationService {
    private final SmsNotificationService smsService;

    public SmsNotificationAdapter(SmsNotificationService smsService) {
        this.smsService = smsService;
    }

    @Override
    public void send(String message) {
        smsService.sendSms(message);
    }
}

@Service
public class Notificator {
    private final List<NotificationService> channels;

    public Notificator(List<NotificationService> channels) {
        this.channels = channels;
    }

    public void notifyAll(String message) {
        for (NotificationService svc : channels) {
            svc.send(message);
        }
    }
}
```


{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Введите общий интерфейс `NotificationService` с методом `send(String message)`.  
💡 Для каждой внешней библиотеки создайте **адаптер** (Adapter), реализующий этот интерфейс и обёртывающий вызовы библиотеки.  
💡 Используйте **конструкторную инъекцию** Spring для подключения внешних сервисов.  
💡 В `Notificator` внедрите `List<NotificationService>` — Spring автоматически соберёт все реализации.  
💡 Чтобы добавить новую библиотеку, достаточно реализовать ещё один адаптер `NotificationService` и зарегистрировать его как Spring-бин.  
{{< /details >}}

{{< details "Решение" close >}}

```java
// 1. Общий интерфейс
public interface NotificationService {
    void send(String message);
}

// 2. Адаптер для Email
@Service
public class EmailNotificationAdapter implements NotificationService {
    private final EmailNotificationService emailService;
    public EmailNotificationAdapter(EmailNotificationService emailService) {
        this.emailService = emailService;
    }
    @Override
    public void send(String message) {
        emailService.sendEmail(message);
    }
}

// 3. Адаптер для SMS
@Service
public class SmsNotificationAdapter implements NotificationService {
    private final SmsNotificationService smsService;
    public SmsNotificationAdapter(SmsNotificationService smsService) {
        this.smsService = smsService;
    }
    @Override
    public void send(String message) {
        smsService.sendSms(message);
    }
}

// 4. Сервис-агрегатор уведомлений
@Service
public class Notificator {
    private final List<NotificationService> channels;
    public Notificator(List<NotificationService> channels) {
        this.channels = channels;
    }
    public void notifyAll(String message) {
        channels.forEach(svc -> svc.send(message));
    }
}

// Использование в приложении
@Component
public class AppRunner implements CommandLineRunner {
    private final Notificator notificator;
    public AppRunner(Notificator notificator) {
        this.notificator = notificator;
    }
    @Override
    public void run(String... args) {
        notificator.notifyAll("Hello, world!");
    }
}
```

{{< /details >}}

