#### 77. Алгоритм фильтрации большой последовательности чисел

🔥 Нужно спроектировать метод, который принимает последовательность чисел до **1 Гб** и возвращает только те значения, которые **> 5**, при этом доступно 1.1 Гб памяти.

---

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Не загружай всю последовательность в память — обрабатывай её **потоково**.  
💡 В Java можно использовать `Stream` или `Iterator`, писать в `OutputStream` или `Writer`.  
💡 Если источник — файл, лучше применять `BufferedReader` + фильтр.  
💡 Если источник — коллекция, и она очень большая, итерируй последовательно, не держа дубликаты.  
💡 Для производительности фильтрацию делай «на лету» без промежуточных списков.  
{{< /details >}}

{{< details "Решение" close >}}

Вариант на Java:

```java
public void filterAndWrite(InputStream input, OutputStream output) throws IOException {
    try (BufferedReader reader = new BufferedReader(new InputStreamReader(input));
         BufferedWriter writer = new BufferedWriter(new OutputStreamWriter(output))) {

        String line;
        while ((line = reader.readLine()) != null) {
            int value = Integer.parseInt(line.trim());
            if (value > 5) {
                writer.write(String.valueOf(value));
                writer.newLine();
            }
        }
    }
}
```

- Читаем вход **построчно** → минимальное потребление памяти.

- Проверяем условие `> 5`.

- Записываем результат в поток (файл, сокет и т.п.).


---

Вариант с коллекцией (итерация без копирования):

```java
public Iterable<Integer> filter(Iterable<Integer> source) {
    return () -> new Iterator<>() {
        private final Iterator<Integer> it = source.iterator();
        private Integer next;

        @Override
        public boolean hasNext() {
            while (it.hasNext()) {
                Integer candidate = it.next();
                if (candidate != null && candidate > 5) {
                    next = candidate;
                    return true;
                }
            }
            return false;
        }

        @Override
        public Integer next() {
            return next;
        }
    };
}
```

- Результат возвращается лениво, без создания промежуточного списка.

- Память тратится только на один текущий элемент.


---

📌 Итого:

- Используем **стримовую/итеративную обработку**.

- Не храним всю выборку в памяти.

- Фильтруем значения **на лету**.


{{< /details >}}
