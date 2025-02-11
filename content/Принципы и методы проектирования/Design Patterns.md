+++
title = 'Паттерны проектирования'
weight = 2
bookFlatSection = true
+++

## Паттерны проектирования

#### 1. GOF паттерны
*GOF паттерны* - это классические шаблоны проектирования, описанные в книге "Design Patterns: Elements of Reusable Object-Oriented Software", написанной четырьмя авторами (так называемой "Бандой четырёх"): Эричем Гаммой, Ричардом Хелмом, Ральфом Джонсоном и Джоном Влиссидесом. Эти паттерны решают распространённые проблемы проектирования в объектно-ориентированном программировании (ООП) и помогают создавать гибкую и поддерживаемую архитектуру.

---
#### 2. Что такое паттерн?
Паттерн - проверенное и готовое к использованию решение

---
#### 3. Плюсы и минусы использования паттернов
**Плюсы**:
- Снижение сложности разработки за счёт готовых абстракций для решения целого класса проблем

**Минусы**:
- Слепое следование некоторому выбранному шаблону может привести к усложнению программы

---
#### 4. Какие существуют группы паттернов?
Паттерны подразделяются на три группы:
- *Порождающие*: помогают создавать объекты.
- *Структурные*: упрощают организацию классов.
- *Поведенческие*: управляют взаимодействием между объектами.

---
#### 5. Какие паттерны в какую группу входят?
- *Порождающие*. Builder, Singleton, Prototype
- *Структурные*. Адаптер. Этот паттерн помогает сделать два несовместимых класса совместимыми друг с другом, действуя как мост между ними
- *Поведенческие*. Цепочка обязанностей, итератор

---
#### 6. Adapter vs Decorator

|Критерий|Adapter|Decorator|
|---|---|---|
|**Цель**|Преобразовать интерфейс одного класса в интерфейс, который ожидает клиент.|Добавить новую функциональность объекту без изменения его структуры.|
|**Пример использования**|Используется, когда у нас есть несовместимые интерфейсы, которые нужно связать.|Используется, когда нужно динамически добавлять новые возможности объектам.|
|**Взаимодействие**|Адаптирует существующий класс к новому интерфейсу.|Оборачивает объект, добавляя или изменяя его поведение.|
|**Структура**|Реализует интерфейс, который ожидает клиент, и делегирует вызовы адаптируемому классу.|Оборачивает объект одного и того же интерфейса, добавляя новую логику.|
|**Пример в реальной жизни**|Электрический адаптер, который преобразует тип розетки.|Подарочная упаковка на коробке, которая не меняет содержимое, но добавляет украшение.|

---
***Паттерн Decorator***

Декоратор — это структурный паттерн проектирования, который позволяет добавлять объектам новые обязанности на лету, помещая их в специальные обёртки.

Ключевые особенности:

- Динамическое добавление функциональности: Не изменяя код исходного объекта.
- Композиция вместо наследования: Декоратор оборачивает объект, вместо того чтобы расширять его через наследование.
- Прозрачность для клиента: Декоратор использует тот же интерфейс, что и объект, который он оборачивает.

---
#### 7. Паттерн MVC
**MVC** (Model-View-Controller) — это архитектурный паттерн, который разделяет приложение на три компонента:
- **Model** — отвечает за данные и логику приложения.
- **View** — отображает данные пользователю (UI).
- **Controller** — управляет взаимодействием пользователя с моделью и обновляет представление.

---
#### 8. Паттерин Command
**Команда (Command)** — это поведенческий паттерн проектирования, который превращает запросы в объекты, позволяя передавать их как аргументы при вызове методов, ставить запросы в очередь, логировать их, а также поддерживать отмену операций.

---
#### 9. Паттерн Chain of responsibility
**Цепочка обязанностей** — это поведенческий паттерн проектирования, который позволяет передавать запросы последовательно по цепочке обработчиков. Каждый последующий обработчик решает, может ли он обработать запрос сам и стоит ли передавать запрос дальше по цепи.

---
#### 10. Как реализуется паттер Abstract Factory?

**Abstract Factory** — это порождающий паттерн проектирования, который предоставляет интерфейс для создания семейств взаимосвязанных объектов без указания их конкретных классов. Он позволяет легко добавлять новые семейства объектов, сохраняя код гибким и расширяемым.

 **Пример реализации Abstract Factory**

**Сценарий:** Предположим, мы создаем интерфейсы для двух семейств GUI-компонентов: Windows и MacOS. Каждое семейство включает кнопку (`Button`) и чекбокс (`Checkbox`).

1. **Интерфейсы для продуктов:**
    
    ```java
    public interface Button {
        void render();
    }
    
    public interface Checkbox {
        void render();
    }
    ```
    
2. **Реализации продуктов для Windows:**
    
    ```java
    public class WindowsButton implements Button {
        @Override
        public void render() {
            System.out.println("Rendering Windows Button");
        }
    }
    
    public class WindowsCheckbox implements Checkbox {
        @Override
        public void render() {
            System.out.println("Rendering Windows Checkbox");
        }
    }
    ```
    
3. **Реализации продуктов для MacOS:**
    
    ```java
    public class MacOSButton implements Button {
        @Override
        public void render() {
            System.out.println("Rendering MacOS Button");
        }
    }
    
    public class MacOSCheckbox implements Checkbox {
        @Override
        public void render() {
            System.out.println("Rendering MacOS Checkbox");
        }
    }
    ```
    
4. **Интерфейс Abstract Factory:**
    
    ```java
    public interface GUIFactory {
        Button createButton();
        Checkbox createCheckbox();
    }
    ```
    
5. **Реализации фабрик для Windows и MacOS:**
    
    ```java
    public class WindowsFactory implements GUIFactory {
        @Override
        public Button createButton() {
            return new WindowsButton();
        }
    
        @Override
        public Checkbox createCheckbox() {
            return new WindowsCheckbox();
        }
    }
    
    public class MacOSFactory implements GUIFactory {
        @Override
        public Button createButton() {
            return new MacOSButton();
        }
    
        @Override
        public Checkbox createCheckbox() {
            return new MacOSCheckbox();
        }
    }
    ```
    
6. **Клиентский код:** Клиент работает с фабрикой через интерфейс, что позволяет легко переключаться между семействами.
    
    ```java
    public class Application {
        private Button button;
        private Checkbox checkbox;
    
        public Application(GUIFactory factory) {
            button = factory.createButton();
            checkbox = factory.createCheckbox();
        }
    
        public void render() {
            button.render();
            checkbox.render();
        }
    }
    
    public class Main {
        public static void main(String[] args) {
            GUIFactory factory;
            String os = "Windows"; // Можно заменить на "MacOS" для другого семейства
    
            if (os.equals("Windows")) {
                factory = new WindowsFactory();
            } else {
                factory = new MacOSFactory();
            }
    
            Application app = new Application(factory);
            app.render();
        }
    }
    ```

---
#### 11. Какие паттерны проектирования используются в Spring


|Паттерн|Описание|Применение в Spring|
|---|---|---|
|**Factory**|Создает объекты через фабричный метод.|BeanFactory и ApplicationContext для создания и управления бинами.|
|**Singleton**|Гарантирует, что существует только один экземпляр класса.|Бины Spring по умолчанию создаются как Singleton.|
|**Prototype**|Создает новый экземпляр объекта при каждом запросе.|Используется для бинов с областью видимости Prototype.|
|**Proxy**|Управляет доступом к объекту через подставной объект.|Реализуется в AOP для аспектов, транзакций, кэширования и других прокси-настроек.|
|**Dependency Injection**|Внедрение зависимостей для уменьшения связности классов.|Основной принцип Spring Framework для управления зависимостями.|
|**Template Method**|Определяет основу алгоритма и позволяет подклассам переопределять определенные шаги.|JdbcTemplate, RestTemplate и другие "шаблоны".|
|**Observer**|Оповещает подписчиков о событиях.|Spring Event Handling для работы с событиями (например, ApplicationEvent).|
|**Adapter**|Преобразует интерфейс класса в другой, ожидаемый клиентом.|HandlerAdapter в Spring MVC.|
|**Decorator**|Добавляет дополнительное поведение объекту динамически.|В Spring AOP для добавления аспектов к методам или классам.|
|**Strategy**|Определяет набор алгоритмов, которые можно заменять друг другом в зависимости от ситуации.|Интерфейсы, такие как `PlatformTransactionManager`, с разными реализациями.|
|**Builder**|Упрощает создание сложных объектов, разделяя их построение на шаги.|`UriComponentsBuilder` для построения URI в Spring Web.|

 **Подробности реализации некоторых паттернов в Spring**

1. **Factory:**
    
    - `BeanFactory` — реализация фабричного паттерна для управления созданием и инициализацией бинов.
    - Пример:
        
        ```java
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        MyBean myBean = context.getBean(MyBean.class);
        ```
        
2. **Proxy:**
    
    - Используется для аспектов (AOP), например, для логгирования, транзакций или кэширования.
    - Прокси-классы создаются динамически с помощью JDK Proxy или CGLIB.
3. **Observer:**
    
    - События, такие как `ContextRefreshedEvent`, используются для уведомления бинов о состоянии контекста.
    - Пример:
        
        ```java
        @Component
        public class MyEventListener implements ApplicationListener<ContextRefreshedEvent> {
            @Override
            public void onApplicationEvent(ContextRefreshedEvent event) {
                System.out.println("Context Refreshed!");
            }
        }
        ```
        
4. **Strategy:**
    
    - Разные реализации интерфейсов используются в зависимости от ситуации.
    - Например, `PlatformTransactionManager` имеет реализации для JPA, JDBC и Hibernate:
        
        ```java
        @Bean
        public PlatformTransactionManager transactionManager(EntityManagerFactory emf) {
            return new JpaTransactionManager(emf);
        }
        ```
        

