## Stream API

#### 1. Что такое Stream Api и для чего нужны стримы?
Представляет собой последовательность элементов, над которой можно производить различные операции. Его задача - упростить работу с наборами данных , в частности, упростить операции фильтрации, сортировки и другие манипуляции данными

#### 2. Почему Stream называют ленивым?
Методы не будут выполняться, пока не будет вызван терминальный метод

#### 3. Какие существуют способы создания стрима?
- Пустой стрим. `Stream.empty() `
- Стрим из List. `list.stream()`
- Стрим из Map. `map.entrySet().stream()`
- Стрим из массива. `Arrays.stream(array)`
- Стрим из указанных элементов. `Stream.of(”1”, “2”, “3”)`
- Конкатенацией двух стримов

#### 4. Что такое промежуточные методы? Какие промежуточные методы в стримах вы знаете?
*Промежуточный метод* - метод, который возвращает Stream. Существуют:

- `filter(Predicate<T> predicate)` Используется для фильтрации элементов потока по условию

```java
List<String> names = List.of("hey", "goodbye", "ok");

List<String> newNames = names.stream()
                .filter(s -> s.length() > 2)
                .toList();
```

- `map(Function<T, R> mapper)` Преобразует каждый элемент потока в другой элемент

```java
List<Integer> numbers = List.of(1,2,3,4);
        numbers.stream()
                .map(i -> i * i)
                .forEach(System.out::println);
```

- `flatMap(Function<T, Stream<R>> mapper)` Разворачивает вложенные структуры, объединяя несколько потоков в один

```java
List<Human> humans = asList(
                new Human("Sam", asList("Buddy", "Lucy")),
                new Human("Bob", asList("Frankie", "Rosie")),
                new Human("Marta", asList("Simba", "Tilly")));

        List<String> petNames = humans.stream()
                .flatMap(human -> human.getPetNames().stream())
                .toList();
                // Вернется список имен домашних животных (Buddy, Lucy и т.д)
```

- `limit(long maxSize)` Ограничивает количество элементов в потоке

```java
List<String> names = List.of("hey", "goodbye", "ok");

        List<String> newNames = names.stream()
                .limit(2)
                .toList();
                // Будет выведено два первых слова
```

- `skip(long n)` Пропускает первые n элементов в потоке

```java
List<String> names = List.of("hey", "goodbye", "ok");

        List<String> newNames = names.stream()
                .skip(2)
                .toList();
                // Будет выведена последняя строка 'ok'
```

- `concat(Stream<T> a, Stream<T> b)` Объединяет два потока в один

```java
List<String> listOne = List.of("Apple", "Banana", "Cherry");
        List<String> listTwo = List.of("Orange", "Peach", "Plum");

        Stream<String> combinedStream = Stream.concat(listOne.stream(), listTwo.stream());
        combinedStream.forEach(System.out::println);
```

- `peek(Consumer<T> action)` Выполняет действие над каждым элементом потока (например, для отладки)

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6, 7);

        List<Integer> result = numbers.stream()
                .peek(num -> System.out.println("Исходное число:" + num))
                .map(num -> num * 2)
                .peek(num -> System.out.println("Число после удвоения" + num))
                .toList();
                // Вывод:
                // Исходное число: 1
                // Число после удвоения: 2 и т.д
```

- `distinct()` Убирает дубликаты, оставляя только уникальные элементы

```java
List<Integer> numbers = asList(1,2,3,4,5,5);

        List<Integer> distinctNumbers = numbers.stream()
                .distinct()
                .toList();
```

- `sorted() / sorted(Comparator<T> comparator)` Использует естественный порядок фильтрации, либо принимает Comparator<T> для сортировки элементов

```java
List<String> words = List.of("hello", "bye", "ok", "goodbye", "andrew");

        List<String> sortedWords = words.stream()
                .sorted(Comparator.comparing(String::length))
                .toList();
```

#### 5. Что такое терминальные методы? Какие терминальные методы в стримах вы знаете?
*Терминальные методы* завершают работу с потоком, после их вызова дальнейшие операции невозможны. Основные терминальные методы:

- `forEach(Consumer<T> action)` Выполняет действие над каждым элементом потока

```java
List<String> words = List.of("hi", "hi", "goodbye", "ok", "dog", "cat", "dog");

        words.stream()
                .filter(w -> w.length() > 2)
                .forEach(System.out::println);
```

- `collect(Collector<T, A, R> collector)` Преобразует элементы потока в другую структуру данных (список, множество и т.д.)

```java
List<String> words = List.of("hi", "hi", "goodbye", "ok", "dog", "cat", "dog");

        Set<String> wordsSet = words.stream()
                .filter(w -> w.length() > 2)
                .collect(Collectors.toSet());
```

- `reduce(BinaryOperator<T> accumulator)` Сворачивает все элементы потока в одно значение, используя бинарную операцию

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5);

        int sum = numbers.stream()
                .reduce(0, Integer::sum);
```

- `count()` Возвращает количество элементов в потоке

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 10, 1298, 27);

        long counted = numbers.stream()
                .filter(n -> n % 2 == 0)
                .count(); // Посчитали четные числа
```

- `min(Comparator<T> comparator)` Возвращает минимальный элемент потока по заданному компаратору

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 10, 1298, 27);

        int max = numbers.stream()
                .min(Integer::compareTo).orElseThrow();
```

- `max(Comparator<T> comparator)` Возвращает максимальный элемент потока по заданному компаратору

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 10, 1298, 27);

        int max = numbers.stream()
                .max(Integer::compareTo).orElseThrow();
```

- `findFirst()` Возвращает первый элемент потока (опционально)

```java
List<Integer> numbers = List.of(1,2,3,4,5,6,10,120,130);

        numbers.stream()
                .filter(n -> n > 10)
                .findFirst()
                .orElseThrow();
```

- `findAny()` Возвращает любой элемент потока (опционально, полезно в параллельных потоках)

```java
List<String> names = List.of("hello java", "goodBye");

        Optional<String> name = names.stream()
                .filter(s -> s.contains("java"))
                .findAny();
```

- `anyMatch(Predicate<T> predicate)` Возвращает true, если хотя бы один элемент соответствует условию

```java
List<Integer> nums = List.of(1,2,3,4,5,6);

        boolean is = nums.stream()
                .anyMatch(n -> n % 2 == 0);
                //Вернет true
```

- `allMatch(Predicate<T> predicate)` Возвращает true, если все элементы соответствуют условию

```java
List<Integer> nums = List.of(-1,2,3,4,5,6);

        boolean is = nums.stream()
                .allMatch(n -> n > 0);
                //Вернет false
```

- `noneMatch(Predicate<T> predicate)` Возвращает true, если ни один элемент не соответствует условию

```java
List<Integer> nums = List.of(1,3,3,5,5,7);

        boolean is = nums.stream()
                .noneMatch(n -> n % 2 == 0);
                //Вернет true (т.к ни одно не удовлетворяет условию)
```

#### 6. Расскажите про метод peek()
Метод `peek()` используется для промежуточной обработки элементов стрима. Он позволяет выполнить действие над каждым элементом стрима без изменения самих элементов. Основное применение — это отладка или логирование данных перед выполнением конечной операции.

*Важно:* метод `peek()` не изменяет поток и чаще всего используется для побочных эффектов (например, вывода информации о каждом элементе).

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
List<Integer> result = numbers.stream()
    .peek(n -> System.out.println("Processing: " + n))
    .map(n -> n * n)
    .collect(Collectors.toList());
System.out.println(result); // [1, 4, 9, 16, 25]
```

#### 7. Расскажите про метод map()
Метод `map()` применяется для преобразования каждого элемента стрима с помощью функции. Он берет входной элемент и возвращает новый элемент, который соответствует результату примененной функции.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
List<Integer> squares = numbers.stream()
    .map(n -> n * n)
    .collect(Collectors.toList());
System.out.println(squares); // [1, 4, 9, 16, 25]
```

#### 8. Расскажите про метод flatMap()
Метод `flatMap()` используется для преобразования каждого элемента стрима в другой стрим, а затем "выравнивания" полученных стримов в один плоский стрим. Это особенно полезно для работы с коллекциями внутри коллекций.

```java
List<List<Integer>> numbers = Arrays.asList(
    Arrays.asList(1, 2),
    Arrays.asList(3, 4),
    Arrays.asList(5)
);
List<Integer> flatNumbers = numbers.stream()
    .flatMap(List::stream)
    .collect(Collectors.toList());
System.out.println(flatNumbers); // [1, 2, 3, 4, 5]
```

#### 9. Чем отличаются методы map() и flatMap()?
- `map()` преобразует каждый элемент в один элемент (или объект).
- `flatMap()` преобразует каждый элемент в стрим и объединяет все полученные стримы в один.

#### 10. Расскажите про метод filter()
Метод `filter()` используется для фильтрации элементов стрима на основе условия (предиката). Он пропускает элементы, которые не удовлетворяют условию.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
List<Integer> evenNumbers = numbers.stream()
    .filter(n -> n % 2 == 0)
    .collect(Collectors.toList());
System.out.println(evenNumbers); // [2, 4]
```

#### 11. Расскажите про метод limit()
Метод `limit()` возвращает стрим, состоящий не более чем из указанного количества элементов. Это полезно для ограничения размера стрима.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
List<Integer> limited = numbers.stream()
    .limit(3)
    .collect(Collectors.toList());
System.out.println(limited); // [1, 2, 3]
```

#### 12. Расскажите про метод skip()
Метод `skip()` пропускает указанное количество элементов и возвращает стрим, начинающийся с оставшихся элементов.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
List<Integer> skipped = numbers.stream()
    .skip(2)
    .collect(Collectors.toList());
System.out.println(skipped); // [3, 4, 5]
```

#### 13. Расскажите про метод sorted()
Метод `sorted()` сортирует элементы стрима. По умолчанию элементы сортируются в естественном порядке, но можно задать компаратор для пользовательской сортировки.

```java
List<String> names = Arrays.asList("John", "Anna", "Tom");
List<String> sortedNames = names.stream()
    .sorted()
    .collect(Collectors.toList());
System.out.println(sortedNames); // [Anna, John, Tom]
```

#### 14. Расскажите про метод distinct()
Метод `distinct()` возвращает стрим, который содержит только уникальные элементы (без дубликатов).

```java
List<Integer> numbers = Arrays.asList(1, 2, 2, 3, 4, 4, 5);
List<Integer> uniqueNumbers = numbers.stream()
    .distinct()
    .collect(Collectors.toList());
System.out.println(uniqueNumbers); // [1, 2, 3, 4, 5]
```

#### 15. Расскажите про метод collect()
Метод `collect()` используется для преобразования стрима в коллекцию или другой тип данных. Чаще всего используется для сбора элементов в список, множество или строку.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
List<Integer> collectedList = numbers.stream()
    .collect(Collectors.toList());
System.out.println(collectedList); // [1, 2, 3, 4, 5]
```

#### 16. Расскажите про метод reduce()
Метод reduce() сводит (агрегирует) элементы стрима к одному значению с помощью функции аккумулятора. Например, для вычисления суммы или произведения всех элементов.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
int sum = numbers.stream()
    .reduce(0, Integer::sum); // 0 — начальное значение
System.out.println(sum); // 15
```

#### 17. Расскажите про класс Collectors и его методы
*Класс Collectors* предоставляет ряд статических методов для создания коллекций, таких как списки, множества, карты и другие, из элементов потока (Stream). Эти методы широко используются для агрегации данных в потоках. Основные методы:

- `toList()` Собирает элементы потока в список
- `toSet()` Собирает элементы потока в множество (Set)
- `toMap()` Собирает элементы потока в карту (Map), используя ключи и значения, которые задаются функциями
- `joining()` Объединяет элементы потока в одну строку, используя заданный разделитель и префикс/суффикс
- `counting()` Считает количество элементов в потоке
- `summarizingInt()` Собирает статистику по числовым значениям (например, сумма, среднее, максимальное и минимальное значение)
- `averagingInt()` Вычисляет среднее значение по числовым элементам
- `partitioningBy()` Разделяет элементы потока на две группы по заданному предикату
- `groupingBy()` Группирует элементы потока по заданному классификатору
- `reducing()` Выполняет редукцию элементов потока с использованием заданного бинарного оператора
- `toCollection(Supplier<C>)` Превращает поток в коллекцию

```java
List<String> names = Arrays.asList("Jaime", "", "Tyron");

        Queue<String> newNames = names.stream()
                .filter(s -> !s.isEmpty())
                .collect(Collectors.toCollection(LinkedList::new));
```

#### 18. Что такое IntStream и DoubleStream?
IntStream и DoubleStream - это специальные стримы в Java для работы с примитивами int и double. Поддерживают дополнительные терминальные методы:

- `sum()`
- `average()`
- `mapToObj()`
