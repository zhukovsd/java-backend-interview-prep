+++
title = 'Java Core'
weight = 1
bookFlatSection = true
+++

## Java Core

#### 1. Расскажи мне про контракт и свойства Equals&Hashcode
***Контракт:***

- Если `hashCode()` двух объектов возвращает разные значения, то они не могут быть равны
- Если `equals()` объектов true, то и хеш-коды должны быть равны
- Переопределив `equals()`, всегда переопределять `hashCode()`
- Если у объектов одинаковый `hashCode()`, то они не обязательно `equals()` 
- При каждом вызове `hashCode()` для одного и того же объекта должен возвращаться один и тот же хеш-код

***Свойства equals():***
 Метод `equals()` реализует отношение равенства (эквивалентности), которое обладает следующими свойствами:
  - **Рефлексивность**. Объект должен быть равен самому себе (`x.equals(x) == true`)
  - **Симметричность**. Любые два объекта должны иметь одно и то же мнение относительно своего равенства или неравенства (`x.equals(y) == true` и `y.equals(x) == true`)
  - **Транзитивность**. Если один объект равен второму, а второй равен третьему, то и первый объект должен быть равен третьему (`x.equals(y) == true` и `y.equals(z) == true` и `x.equals(z) == true`)
  - **Непротиворечивость**. Если два объекта равны, они должны быть равны всегда, пока один из них (или оба они) не будет изменен
  - Для любой ненулевой ссылки на значение `х` выражение `х.equals(null)` должно возвращать `false`.

  ***Свойства hashCode():***
  - **Согласованность**. При каждом вызове `hashCode()` для одного и того же объекта в рамках одной программы должно возвращаться одно и то же значение, если поля объекта, участвующие в расчете хеш-кода, не изменились
  - **Равенство и хеш-код.** Если два объекта равны по методу `equals()`, их хеш-коды тоже должны быть равны. Однако обратное не обязательно — объекты с одинаковым хеш-кодом могут быть не равны
  - **Распределение**. Реализация `hashCode()`, возвращающая разный хеш-код для неравных объектов позволяет увеличить эффективность работы с хеш-структурами данных (такими как HashMap или HashSet) за счёт распределения объектов по разным корзинам хеш-таблицы

---
#### 2. Что такое коллизия?
 Коллизия возникает, когда у разных объектов одинаковые хеш-коды

---
 ***Что будет, если переопределить equals() не переопределяя hashCode()?***
- Объект, который по `equals()` равен существующему ключу в HashMap, может не быть найден, если их хеш-коды различаются. Это связано с тем, что HashMap сначала использует `hashCode()` для поиска корзины, а уже потом сравнивает объекты по `equals()`.
- При попытке удалить ключ, который равен другому ключу по `equals()`, но имеет другой `hashCode()`, удаление может не сработать.

---
#### 3. Какие типы ссылок существуют в Java?
- **Сильная ссылка.** Обычная ссылка, которая повсеместно используется (`Object obj = new Object()`). Объект не будет удален сборщиком мусора, пока на него есть хотя бы одна сильная ссылка
- **Мягкая ссылка.** Используются для кеширования. Объект, на который ссылается мягкая ссылка, будет удален сборщиком мусора только при нехватке памяти (`SoftReference<MyObject> softRef = new SoftReference<>(new MyObject())`)
- **Слабая ссылка.** Объект, на который ссылается слабая ссылка, может быть удален сборщиком мусора при следующей проверке, даже если на него есть слабые ссылки (`WeakReference<MyObject> weakRef = new WeakReference<>(new MyObject())`)
- **Фантомная ссылка**. Используется для отслеживания, когда объект был удален из памяти. Фантомная ссылка не позволяет получить доступ к объекту напрямую, она только сигнализирует, что объект скоро будет удален (`PhantomReference<MyObject> phantomRef = new PhantomReference<>(new MyObject(), referenceQueue)`)

---
#### 4. Какой класс называют Immutable?
  Иммутабельный класс - класс, экземпляры которого не могут быть изменены после создания. Принципы создания:
  - Поля private `final`  
  - Не предоставляйте методы, которые изменяют состояние объекта (никаких setter-ов)
  - Гарантируйте невозможность расширения класса (класс делаем `final`)
  - Все поля устанавливаются через конструктор
  - Обеспечьте монопольный доступ ко всем изменяемым компонентам. Если класс имеет любые поля, ссылающиеся на изменяемые объекты, убедитесь, что клиенты класса не смогут получить ссылки на эти объекты (защитные копии `(defensive copies)`, использование иммутабельных объектов).

---
#### 5. Основная идея языка
 Написано однажды - работает везде

---
#### 6. За счет чего обеспечена кроссплатформенность?
 За счет создания виртуальной машины Java. Это что-то вроде прослойки между ОС и Java программой

---
#### 7. Плюсы и минусы Java?
***Плюсы:***
- **Объектно-ориентированное программирование.** Структура данных становится объектом, которым можно управлять, создавать отношения между различными объектами.

- **Язык высокого уровня с простым синтаксисом и плавной кривой обучения.** Синтаксис Java основан на C ++, поэтому Java похожа на C. Тем не менее, синтаксис Java проще, что позволяет новичкам быстрее учиться и эффективнее использовать код для достижения конкретных результатов.
- **Стандарт для корпоративных вычислительных систем.**  Корпоративные приложения — главное преимущество Java с 90-х годов, когда организации начали искать надежные инструменты программирования не на C.
- **Безопасность.**  Благодаря отсутствию указателей и Security Manager (политика безопасности, в которой можно указать правила доступа, позволяет запускать приложения Java в "песочнице").
- **Независимость от платформы.**  Можно создать Java-приложение на Windows, скомпилировать его в байт-код и запустить его на любой другой платформе, поддерживающей виртуальную машину Java (JVM). Таким образом, JVM служит уровнем абстракции между кодом и оборудованием.
- **Язык для распределенного программирования и комфортной удаленной совместной работы.**   Специфическая для Java методология распределенных вычислений называется Remote Method Invocation (RMI). RMI позволяет использовать все преимущества Java: безопасность, независимость от платформы и объектно-ориентированное программирование для распределенных вычислений. Кроме того, Java также поддерживает программирование сокетов и методологию распределения CORBA для обмена объектами между программами, написанными на разных языках.
- **Автоматическое управление памятью.** (вопрос спорный: плюс или минус) Разработчикам Java не нужно вручную писать код для управления памятью благодаря автоматическому управлению памятью (AMM).
- **Многопоточность.** Поток — наименьшая единица обработки в программировании. Чтобы максимально эффективно использовать время процессора, Java позволяет запускать потоки одновременно, что называется многопоточностью.
- **Стабильность и сообщество.**  Сообщество разработчиков Java не имеет себе равных. Около 45% респондентов опроса StackOverflow 2018 используют Java.

***Минусы:***
- Платное коммерческое использование (с 2019)
- Многословный код

---
#### 8. JVM, JRE, JDK, JIT
- **JVM** - Виртуальная машина Java, которая исполняет байт-код
- **JRE** - Среда выполнения Java. Включает в себя JVM и стандартные библиотеки классов 
  - java.lang - String, System, Object
  - java.io - InputStream, OutputStream
  - java.util - коллекции (List, Set, Map)
- **JDK** - Набор инструментов для разработки. Включает в себя JRE, компилятор и другие утилиты
- **JIT** - компиляция байт-кода в машинный во время выполнения программы

---
#### 9. Что такое байт-код?
Набор инструкций исполняемый виртуальной машиной Java

---
#### 10. Что такое сборщик мусора (garbage collector)?
В Java используется автоматическое управление памятью. Программист выделяет память, а за освобождение отвечает JVM. Когда программа больше не ссылается на объект (прямые или косвенные ссылки), то объект удаляется, а память переиспользуется. Сборщик мусора – это демон-поток, который выполняет две задачи:
1. поиск 
2. очистка мусора.

---
***Вызов сборщика мусора сейчас?*** 
- system.gc()

---
***Разница между GC в разных версиях Java***

| **Версия Java** | **GC по умолчанию** | **Примечания**                                              |
|-----------------|---------------------|-------------------------------------------------------------|
| Java 8          | 	Parallel GC        | 	Оптимизирован для пропускной способности.                  |
| Java 9          | 	G1                 | 	G1 стал сборщиком по умолчанию, заменив Parallel GC.       |
| Java 10         | 	G1                 | 	Улучшения в G1, включая параллельную полную сборку мусора. |
| Java 11         | 	G1                 | 	Введены ZGC и Shenandoah (экспериментальные).              |
| Java 12         | 	G1                 | 	Улучшения в G1, включая возврат неиспользуемой памяти.     |
| Java 13         | 	G1                 | 	Улучшения в ZGC и Shenandoah.                              |
| Java 14         | 	G1                 | 	ZGC и Shenandoah стали стабильными.                        |
| Java 15         | 	G1                 | 	ZGC стал кроссплатформенным.                               |
| Java 16         | 	G1                 | 	Улучшения в ZGC и Shenandoah.                              |
| Java 17         | 	G1                 | 	CMS окончательно удален.                                   |
| Java 21         | 	G1                 | 	Введен Generational ZGC, улучшения в G1, ZGC и Shenandoah. |

***Когда использовать другие GC?***

* *ZGC*: Для приложений с огромными кучами (терабайты) и требованиями к минимальным паузам (менее 10 мс).
* *Shenandoah*: Для приложений с низкими задержками и большими кучами.
* *Parallel GC*: Для приложений, где важна пропускная способность, а не низкие задержки.
* *Serial GC*: Для однопоточных приложений или приложений с небольшими кучами.

---
***Как работает GC?***

1. Поиск недостижимых объектов: Проверяет, есть ли ссылки на объект.
2. Очистка: Если объект недостижим, его память освобождается.
3. Обновление памяти: Освобождённая память становится доступной для новых объектов.

Основные алгоритмы GC:

- Mark-and-Sweep: Отмечает используемые объекты и удаляет неиспользуемые.
- Generational GC: Разделяет память на молодое, старшее и постоянное поколение для оптимизации работы.

---
***Какие виды GC знаешь?***

1. Serial GC: Подходит для небольших приложений (однопоточный).
2. Parallel GC: Использует несколько потоков, подходящий для многопоточных приложений.
3. CMS (Concurrent Mark-Sweep): Быстрый сбор мусора, минимизирует паузы.
4. G1 GC (Garbage First): Балансирует между производительностью и паузами.
5. ZGC: Для приложений с низкими паузами, работающими с большим объёмом памяти.
6. Shenandoah GC: Минимизирует паузы сборщика мусора.

---
#### 11. Области памяти Java
**Stack** - это область хранения данных. Всякий раз, когда вызывается метод, в памяти стека создается новый блок, который содержит примитивы и ссылки на другие объекты в методе. Как только метод заканчивает работу, блок также перестает использоваться, тем самым предоставляя доступ для следующего метода. Стек в Java работает по схеме LIFO (Последний зашел - Первый вышел)

**Heap** (куча) используется для выделения памяти под объекты и классы. Создание нового объекта также происходит в куче. Любой объект, созданный в куче, имеет глобальный доступ и на него могут ссылаться из любой части приложения.


| Область памяти         | Описание                                                                                         |
|------------------------|--------------------------------------------------------------------------------------------------|
| **Heap (Куча)**        | Для динамического распределения объектов и классов. Управляется сборщиком мусора.                |
| **Stack (Стек)**       | Хранит локальные переменные и вызовы методов. Каждый поток имеет свой собственный стек.          |
| **Method Area**        | Хранит информацию о классах (имена классов, методы, переменные). Общая для всех потоков.         |
| **PC Register**        | Хранит адрес текущей инструкции, выполняемой потоком. Каждый поток имеет свой собственный регистр.|
| **Native Method Stack**| Для выполнения нативных методов (написанных на других языках).                                   |
| **Code Cache**         | Хранит скомпилированный JIT-код для повышения производительности.                                 |
| **Metaspace**          | Для хранения метаданных классов. Заменяет Permanent Generation (PermGen).                         |
                              |


---
***В чём разница между Stack и Heap?***

- Куча больше стека
- Стек хранит примитивы и ссылки на объекты, а куча хранит все созданные объекты
- Память стека существует пока выполняется текущий метод, а куча существует пока работает приложение
- Стек быстрее кучи

---
***В какой области памяти содержатся типы данных?***

|**Тип данных**|**Расположение**|
|---|---|
|Примитивные типы|В стеке (локальные переменные) или куче (поля объекта).|
|Объекты|В куче.|
|Ссылки на объекты|В стеке.|
|Метаданные классов|В Metaspace.|

---
#### 12. Типы данных в Java
В Java есть примитивные и ссылочные типы данных.

**Примитивные типы данных:**
- byte (0) (8 бит)
- short (0) (16 бит)
- int (0) (32 бит)
- long (0L) (64 бит)
- float (0.0f) (32 бит)
- double (0.0d) (64 бит)
- char (\u0000) (16 бит)
- boolean (false) (зависит от JVM)

---
#### 13. Что такое класс-обертка?
Обертка - специальный класс, который хранит внутри себя значения примитива. Обертки являются Immutable

---
#### 14. Автоупаковка и автораспаковка
- Автоупаковка - примитивный тип ⇒ объектный класс
- Автораспаковка - объектный класс ⇒ примитивный тип

---
#### 15. Строки/String в Java? Из чего состоит? Нюансы?
Класс String представляет строковый набор символов.

Класс String - immutable из-за модификатора final и отсутствия сеттера. При редактировании строк будет создаваться новая строка

---
#### 16. Что такое пул строк/String pool?
Пул строк — это особая область кучи (heap), где хранятся объекты строк. Служит в первую очередь для оптимизации работы со строковыми литералами. При объявлении литерала (строки в двойных кавычках) в пуле происходит поиск этой строки:
- Если такой строки в пуле нет ⇒ создается строка, возвращается ссылка
- Если есть ⇒ возвращается ссылка найденной строки  

Строки, созданные с помощью `new String()` не помещаются в пул строк и хранятся в куче как остальные объекты.

---
***Что делает метод intern() в классе String?***
- Помещает строку в пул строк

---
#### 17. Почему не рекомендуется изменять строки в цикле?
Строка является неизменной и финализированной в Java, поэтому все наши манипуляции со строкой всегда будут создавать новую строку. Манипуляции со строками ресурсоемкие, поэтому Java обеспечивает два полезных класса для манипуляций со строками – **StringBuffer** и **StringBuilder**.

---
#### 18. Разница между String, StringBuffer, StringBuilder
- **String** - неизменяемый, потокобезопасный
- **StringBuffer** - изменяемый, потокобезопасный
- **StringBuilder** - изменяемый, потоко**НЕ**безопасный

---
#### 19. Каким образом переменные передаются в методы — по значению или по ссылке?
По значению. Всегда

---
#### 20. Что такое enum?
Enum - набор логически связанных констант. Нужны для ограничения области допустимых значений (времена года, дни недели)

---
#### 21. Что такое конструктор?
Конструкторы в Java представляют собой специальный блок кода, похожий на метод, предназначенный для инициализации полей объекта при его создании. Он вызывается всегда, когда создается новый экземпляр класса.

---
***Конструктор по умолчанию***
  
Если у класса не определить конструктор, то компилятор сгенерирует конструктор без аргументов - так называемый “конструктор по умолчанию”

---
***Для чего нужны приватные/private конструкторы?***
Приватный конструктор запрещает создание экземпляра класса вне методов самого класса. Нужен для реализации паттернов `singleton`

```java
public class Singleton {
    // Статическое поле для хранения единственного экземпляра класса
    private static Singleton instance;

    // Приватный конструктор, предотвращающий создание экземпляров извне
    private Singleton() {}

    // Публичный метод для доступа к единственному экземпляру класса
    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

---
***Чем отличаются конструкторы по умолчанию, конструктор копирования и конструктор с параметрами?***
- Конструктор по умолчанию. Отсутствуют какие-либо параметры
- Конструктор копирования. Принимает в качестве аргумента уже существующий объект класса для последующего создания его клона
- Конструктор с параметрами имеет в своей сигнатуре аргументы

---
#### 22. Какие есть модификаторы доступа? Какие применимы к классам?
- `private`. Доступ только из класса, в котором объявлен
- `default`. Переменная доступна для любого другого класса в том же пакете
- `protected`. Переменная доступна всем классам внутри пакета, а так же всем классам-наследникам вне пакета
- `public`. Доступ к компоненту из любой точки программы

---
#### 23. Что означает static?
- `static` переменная - переменная, принадлежащая классу, а не объекту
- `static` класс - вложенный класс, который может обращаться только к статическим полям класса-обертки
- `static` метод - метод, принадлежащий классу, а не объекту. Статические методы обращаются только к другим статическим методам и переменным

---
***Может ли статический метод быть переопределен?***

Нельзя переопределять статические методы.
Если объявить такой же метод в классе-наследнике, то мы лишь «спрячем» метод суперкласса вместо переопределения. Это называется сокрытие методов.

> *Прим.* Перегружать можно, переопределять нет. В случае со статическими методами это не переопределение (overriding), а сокрытие (hiding).

---
***Могут ли нестатические методы перегружать статические?***

Да. В итоге получится два разных метода. 
Статический будет принадлежать классу и будет доступен через его имя, а нестатический будет принадлежать конкретному объекту и доступен через вызов метода этого объекта.

---
***Могут ли классы быть статическими?***

Да, вложенные классы

---
#### 24. Можно ли сузить уровень доступа или тип возвращаемого значения при переопределении метода?
Можно расширить модификатор доступа и сузить тип возвращаемого значения (если они совместимы)

---
#### 25. Что можно изменить в сигнатуре метода при переопределении? Можно ли менять модификаторы (throws и т.п.)?
- В сигнатуре менять ничего нельзя. 
- Можно расширить уровень доступа. 
- Можно сузить тип возвращаемого значения. 
- Можно не указывать исключения, которые были объявлены в родительском классе.
- Можно добавить новые исключения, которые являются наследниками родительского, либо те, что наследуются от RuntimeException

---
#### 26. Что означает модификатор final? К чему он может быть применим?
- `final` у класса. Запрещает наследование
- `final` у метода. Запрещает переопределять данный метод в наследниках класса
- `final` у примитива. Запрещает изменение значения после присвоения
- `final` у ссылочной переменной. Запрещает изменение ссылки на объект
- `final` у массива. Запрещает изменение ссылки на массив (но можно менять элементы внутри)

---
#### 27. Что такое абстрактные классы?
Абстрактным классом называется класс, у которого не может быть экземпляров. Может содержать абстрактные методы. Наследниками абстрактного класса могут быть другие абстрактные классы и обычные классы

---
***Может ли быть абстрактный класс без абстрактных методов?***

Да, может

---
***Могут ли быть конструкторы у абстрактных классов? Для чего они нужны?***

Да, могут, если они нужны для наследников

---
#### 28. Что такое интерфейсы?
Интерфейс — это особый абстрактный тип, описывающий поведение (контракт), которое  классы должны реализовать
- Методы интерфейса - public abstract
- Поля - public static final

---
***Какие модификаторы по умолчанию/по дефолту имеют поля и методы интерфейсов?***

Default методы - это методы в интерфейсе, которые имеют тело и помечены `default`. Классы, реализующие такой интерфейс могут не переопределять метод и использовать код из интерфейса.

Начиная с Java 8 в интерфейсах разрешается размещать реализацию методов по умолчанию `default` и статических `static` методов.

По умолчанию все методы в интерфейсе фактически имеют модификатор `public`. Однако начиная с Java 9 мы также можем определять в интерфейсе методы с модификатором `private`. Они могут быть статическими и нестатическими, но обязаны иметь реализацию (т.е. тело метода).

Интерфейс также может содержать и поля. В этом случае они автоматически являются публичными `public`, статическими `static` и неизменяемыми `final`.

---
***Может ли один интерфейс наследоваться от другого? От двух других?***

Да

---
***Зачем нужны интерфейсы маркеры?***

Интерфейсы-маркеры (marker interfaces) — это интерфейсы без методов, используемые для предоставления метаинформации о классе.

**Примеры:**

`Serializable` — сообщает, что объект может быть сериализован.  
`Cloneable` — разрешает создание копий объекта через clone().  

**Плюсы:**  
✅ Простота реализации.  
✅ Используется в стандартной библиотеке Java (Serializable, Cloneable).  
✅ Позволяет использовать instanceof для проверки типа.  

**Минусы:**  
❌ Неявная логика — тяжело понять, что делает интерфейс.  
❌ Сложно поддерживать, если нужно добавить новую функциональность.  
❌ Нельзя контролировать правильность использования (например, Cloneable не гарантирует корректную реализацию clone()).

---
***Плюсы и минусы дефолтных методов***

**`default`-методы** позволяют добавлять реализацию методов в интерфейсы без необходимости изменять существующие реализации классов.

**Пример:**

```java
interface Animal {
    default void makeSound() {
        System.out.println("Some sound...");
    }
}
class Dog implements Animal {}
public class Main {
    public static void main(String[] args) {
        Dog dog = new Dog();
        dog.makeSound(); // Выведет "Some sound..."
    }
}
```

**Плюсы:**  
✅ Позволяют добавлять новые методы в интерфейсы без ломания существующего кода.  
✅ Уменьшают дублирование кода в классах, реализующих интерфейс.  
✅ Можно предоставлять базовую реализацию метода.

**Минусы:**  
❌ Может усложнять иерархию классов, если использовать слишком активно.  
❌ Конфликты `default`-методов, если несколько интерфейсов содержат метод с одинаковой сигнатурой.    
❌ Нарушает принцип единственной ответственности (SRP), так как интерфейс начинает содержать логику.

---
***Плюсы и минусы множественной имплементации***

Множественная имплементация означает, что класс может реализовать несколько интерфейсов.

**Пример:**

```java
interface A {
    default void show() {
        System.out.println("A");
    }
}
interface B {
    default void show() {
        System.out.println("B");
    }
}
class C implements A, B {
    @Override
    public void show() {  // Разрешаем конфликт
        A.super.show();
    }
}
```

**Плюсы:**  
✅ Позволяет избежать проблем, связанных с множественным наследованием (алмазная проблема).  
✅ Повторное использование кода через `default`-методы.  
✅ Гибкость — можно комбинировать поведение разных интерфейсов.

**Минусы:**  
❌ Конфликты `default`-методов, если несколько интерфейсов содержат метод с одинаковой сигнатурой.  
❌ Возможна сложность в отладке и поддержке кода.  
❌ Нельзя использовать `super` для вызова конструктора интерфейса.

---
#### 29. Отличия интерфейса от абстрактных классов?

- Абстрактные классы используются, когда есть отношение ‘is a’, а интерфейс может быть реализован классами, вовсе не связанными друг с другом
- Унаследовать можно только один класс, а реализовать интерфейсов — сколько угодно 

|**Критерий**|**Абстрактный класс**|**Интерфейс**|
|---|---|---|
|**Когда использовать**|Если есть общая логика, которую наследники могут переопределять|Для определения контрактов, которые реализуют классы|
|**Модификаторы доступа по умолчанию**|`protected`, `default`|Все методы `public`|
|**Наличие конструктора**|Да|Нет|
|**Наследование/реализация**|Класс может наследовать только один абстрактный класс|Класс может реализовать несколько интерфейсов|
|**Методы с реализацией**|Может содержать методы с реализацией|С Java 8: может содержать `default` методы с реализацией|

---
#### 30. Каков порядок вызова конструкторов и  блоков инициализации с учетом иерархии классов?
1. Статические блоки у родителя
2. Статический блок у наследника
3. Нестатический блок у родителя
4. Конструктор родителя
5. Нестатические блоки у наследника
6. Конструктор наследника

---
#### 31. Зачем нужны и какие бывают блоки инициализации?
Существуют статические и нестатические блоки инициализации.  
**Статический блок инициализации** используется для инициализации статических данных класса. Он выполняется только один раз, когда класс загружается в память.  
**Нестатический блок инициализации**, также известный как блок инициализации экземпляра, используется для инициализации переменных экземпляра класса. Он выполняется каждый раз, когда создается новый экземпляр класса.

---
#### 32. Для чего в Java используются статические блоки инициализации?
Статический блок инициализации используется для инициализации статических данных класса. Он выполняется только один раз, когда класс загружается в память.  

---
#### 33. Класс Object и его методы?
Object - базовый класс для всех объектов в Java. Любой класс так или иначе наследуется от `Object`, и, соответственно, наследует его методы (11 штук):
- `equals()` - определяет, равен ли один объект другому.
- `hashCode()` - возвращает хэш-код, связанный с вызывающим объектом.
- `toString()` - возвращает символьную строку, описывающую объект.
- `getClass()` - получает класс объекта во время выполнения.
- `clone()` - создает новый объект, не отличающийся от клонируемого.
- `finalize()` - вызывается перед удалением неиспользуемого объекта.

А так же методы для многопоточности:
- `notify()` - возобновляет исполнение потока, ожидающего вызывающего объекта.
- `notifyAll()` - возобновляет исполнение всех потоков, ожидающих вызывающего объекта.
- `wait()`, `wait(long timeout)`, `wait(long timeout, int nanos)` - ожидает другого потока исполнения.

---
***Метод finalize()***

`finalize()` — это метод в классе `Object`, который вызывается перед уничтожением объекта сборщиком мусора (GC). Помечен как `@Deprecated` с Java 9 с возможностью дальнейшего удаления.

**Пример использования:**

```java
class Resource {
    @Override
    protected void finalize() throws Throwable {
        System.out.println("Объект удаляется");
    }
}
public class Main {
    public static void main(String[] args) {
        new Resource();
        System.gc(); // Запрос на сборку мусора
    }
}
```

**Плюсы `finalize()`:**  
✅ Можно освободить ресурсы перед удалением объекта.

**Минусы `finalize()`:**  
❌ Не гарантируется, что `finalize()` вызовется своевременно.  
❌ Сложно отлаживать, так как GC работает асинхронно.  
❌ Медленный — лучше использовать `try-with-resources` или `close()`.

**Современная альтернатива:**  
Java 9+: использовать `Cleaner` или `try-with-resources`.

---
#### 34. Что такое сериализация и для чего она нужна?
Сериализация - это процесс сохранения объекта в последовательность байт. Реализована через интерфейс-маркер Serializable. Нужна для компактного сохранения состояния объекта и считывания этого состояния

---
#### 35. Разница между версиями Java

- **Java 8**: Лямбда-выражения, Stream API, Optional API, новый Date and Time API, `default` методы в интерфейсах.
- **Java 9**: Модульная система (JPMS), улучшения в Stream API и методы `Optional.ifPresentOrElse`, фабричные методы для коллекций (`List.of`).
- **Java 10**: Локальная типизация (`var`), улучшения в GC, API для создания неизменяемых коллекций (`List.copyOf, Set.copyOf, Map.copyOf`)
- **Java 11**: Поддержка `var` в лямбдах, новый клиент HTTP.
- **Java 17**: `Record`, `sealed` классы, улучшения в GC и performance, pattern matching для `instanceof`.
- **Java 21**: Virtual threads, pattern matching для `switch`, поддержка sealed-иерархий для `record`, улучшения в GC

---
#### 36. Может ли абстрактный класс быть final?

Нет. Ключевое слово final запрещает наследование класса, а абстрактный класс предназначен для наследования.

---
#### 37. Integer pool

Integer Pool — это кеширование объектов типа Integer в диапазоне от -128 до 127 для уменьшения количества создаваемых объектов.

---
#### 38. Какие виды загрузчиков классов ты знаешь?

- **Bootstrap ClassLoader**: Загружает стандартные библиотеки (например, `rt.jar`).
- **Extension ClassLoader**: Загружает дополнительные библиотеки (`ext`).
- **System ClassLoader**: Загружает классы из classpath.
- **Custom ClassLoader**: Пользовательские загрузчики классов.

---
#### 39. Record-ы c Java 17. Для чего и зачем?

**Record** — это компактная декларация неизменяемого класса, удобная для хранения данных.

- **Преимущества**:
    - Автоматическое создание конструктора, getter-ов (методы именуется без префикса get), `equals`, `hashCode`, `toString`.
    - Неизменяемость полей.

```java
public record Point(int x, int y) {}
Point p = new Point(1, 2);
System.out.println(p.x()); // 1
```

---
#### 40. Что такое Анонимный класс

*Анонимный класс* — это класс без имени, который создаётся и используется "на месте".

```java
Runnable r = new Runnable() {
    @Override
    public void run() {
        System.out.println("Hello!");
    }
};

```

---
***Требования к переменным, на которые ссылаемся внутри анонимного класса***

Переменные, используемые внутри анонимного класса, должны быть **effectively final** (не изменяться после инициализации).

---
#### 41. Расскажи что делает JIT компилятор

JIT (Just-In-Time) компилятор преобразует байт-код в машинный код во время выполнения программы для повышения производительности. 

---
#### 42. Расскажи про прогрев JVM

**Прогрев JVM** — это процесс оптимизации приложения, когда JIT анализирует исполняемый код и применяет оптимизации, такие как инлайнинг или устранение лишних проверок.

---
#### 43. break vs continue в цикле

- **`break`**: Завершает выполнение текущего цикла.
- **`continue`**: Пропускает текущую итерацию цикла и переходит к следующей.

---
#### 44. Что такое аннотация? Зачем нужна? Как создать свою аннотацию?

Аннотации в Java — это специальный механизм, который позволяет добавлять метаданные к коду (классам, методам, полям и т. д.). Они не изменяют поведение кода напрямую, но активно используются компилятором, инструментами разработки и фреймворками.

**Основные аннотации в Java**  


| Аннотация      | Описание |
|---------------|----------|
| `@Override`   | Проверяет, что метод переопределяет метод суперкласса. |
| `@Deprecated` | Отмечает метод/класс устаревшим, чтобы не использовать его в будущем. |
| `@SuppressWarnings` | Подавляет определенные предупреждения компилятора. |
| `@FunctionalInterface` | Показывает, что интерфейс является функциональным (с одним абстрактным методом). |
| `@SafeVarargs` | Подавляет предупреждения о небезопасных операциях с аргументами переменной длины (varargs). |
| `@Native` | Используется для констант, предназначенных для взаимодействия с нативным кодом. |

**Примеры встроенных аннотаций**  

```java
class Parent {
    void display() {}
}

class Child extends Parent {
    @Override  // Компилятор проверит, что метод действительно переопределяет родительский.
    void display() {
        System.out.println("Переопределенный метод");
    }

    @Deprecated  // Компилятор выдаст предупреждение, если этот метод будет использоваться.
    void oldMethod() {
        System.out.println("Устаревший метод");
    }

    @SuppressWarnings("unchecked")  // Подавляет предупреждения о небезопасных операциях.
    void uncheckedMethod() {
        System.out.println("Небезопасная операция");
    }
}
```

---

**Метаданные аннотаций (`Retention`, `Target`)**  
Когда мы создаем свою аннотацию, важно понимать, как она будет использоваться. Это задается с помощью **мета-аннотаций**:

| Аннотация | Описание |
|-----------|----------|
| `@Retention` | Определяет, на каком этапе жизненного цикла аннотация доступна (SOURCE, CLASS, RUNTIME). |
| `@Target` | Указывает, к чему можно применять аннотацию (методы, поля, классы и т. д.). |
| `@Documented` | Включает аннотацию в Javadoc. |
| `@Inherited` | Наследуется ли аннотация подклассами. |
| `@Repeatable` | Позволяет применять одну и ту же аннотацию несколько раз. |

**Как создать свою аннотацию?**  

```java
import java.lang.annotation.*;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@interface MyAnnotation {
    String value();
    int count() default 1;
}

class Example {
    @MyAnnotation(value = "Hello", count = 3)
    public void test() {
        System.out.println("Метод с аннотацией");
    }
}
```

**Чтение аннотации с помощью рефлексии**  

```java
import java.lang.reflect.Method;

public class Main {
    public static void main(String[] args) throws Exception {
        Method method = Example.class.getMethod("test");
        MyAnnotation annotation = method.getAnnotation(MyAnnotation.class);
        System.out.println("value: " + annotation.value());  // "Hello"
        System.out.println("count: " + annotation.count());  // 3
    }
}
```

**Где используются аннотации в реальных проектах?**  

**Lombok (`@Getter`, `@Setter`)**  

Автоматически генерирует геттеры и сеттеры.

```java
import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
class Person {
    private String name;
}
```

**Spring Boot (`@RestController`, `@Autowired`)**  
Используется для конфигурирования компонентов.

```java
import org.springframework.web.bind.annotation.*;

@RestController
public class MyController {
    @GetMapping("/hello")
    public String hello() {
        return "Hello, world!";
    }
}
```

**JUnit (`@Test`)**  
Позволяет помечать методы как тесты.

```java
import org.junit.jupiter.api.Test;

class MyTests {
    @Test
    void testSomething() {
        System.out.println("Запуск теста");
    }
}
```

---
#### 45. Переопределение и перегрузка методов

 🔁 **Переопределение методов**

Это **изменение поведения метода**, унаследованного от родительского класса.

 📌 Правила:

- Метод должен иметь **точно такое же имя**, **тип возвращаемого значения** и **аргументы**.  
- Метод в родительском классе **должен быть не private**.  
- Должен происходить в **наследуемом классе**.   
- Аннотация `@Override` **рекомендуется** (не обязательна, но помогает избежать ошибок).   

 📦 Пример:

```java
class Animal {
    public void makeSound() {
        System.out.println("Животное издает звук");
    }
}

class Dog extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Собака лает 🐶");
    }
}
```

 🧠 Что происходит:

Метод `makeSound()` переопределён в классе `Dog`. При вызове на объекте типа `Dog` будет работать именно его версия.

 ➕ **Перегрузка методов (Overloading)**

Это когда в одном классе есть **несколько методов с одинаковым именем**, но **разными параметрами**.

 📌 Правила:
- Имена методов **одинаковые**.  
- Различие — в **количестве**, **типах** или **порядке аргументов**.  
- Возвращаемый тип **может быть любым**, но **не используется для различия перегрузки**.  

 📦 Пример:

```java
class Calculator {

    public int add(int a, int b) {
        return a + b;
    }

    public double add(double a, double b) {
        return a + b;
    }

    public int add(int a, int b, int c) {
        return a + b + c;
    }
}
```

 🧠 Что происходит:

Метод `add()` перегружен — Java понимает, какой использовать, в зависимости от аргументов.



