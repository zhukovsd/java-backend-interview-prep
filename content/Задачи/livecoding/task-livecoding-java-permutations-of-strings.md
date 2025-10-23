#### 86. Являются ли строки перестановками друг друга

**Условие задачи:**  
📌 Нужно написать метод, который проверяет являются ли строки перестановками друг друга?

**Код:**

```java
// Напишите метод arePermutations(String a, String b)

System.out.println(arePermutations("abc", "bca"));   // true
System.out.println(arePermutations("aab", "aba"));   // true
System.out.println(arePermutations("abc", "abcc"));  // false
System.out.println(arePermutations("", ""));         // true
System.out.println(arePermutations(null, "abc"));    // false
```

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Сразу отсекайте разные длины.  
💡 Подход 1 (простой): отсортировать массивы символов и сравнить.  
💡 Подход 2 (линейный): посчитать частоты символов (Map) и сверить.  
💡 Если нужно игнорировать регистр/пробелы — нормализуйте входные строки перед сравнением.  
{{< /details >}}

{{< details "Решение" close >}}

```java
// Вариант 1: через сортировку (O(n log n), простой и надёжный)
static boolean arePermutations(String a, String b) {
    if (a == null || b == null) return false;
    if (a.length() != b.length()) return false;

    char[] ca = a.toCharArray();
    char[] cb = b.toCharArray();
    Arrays.sort(ca);
    Arrays.sort(cb);
    return Arrays.equals(ca, cb);
}

// Вариант 2: через частоты символов (O(n), без доп. сортировки)
static boolean arePermutationsLinear(String a, String b) {
    if (a == null || b == null) return false;
    if (a.length() != b.length()) return false;

    Map<Character, Integer> freq = new HashMap<>();
    for (char c : a.toCharArray()) {
        freq.merge(c, 1, Integer::sum);
    }
    for (char c : b.toCharArray()) {
        Integer cnt = freq.get(c);
        if (cnt == null) return false;
        if (cnt == 1) freq.remove(c);
        else freq.put(c, cnt - 1);
    }
    return freq.isEmpty();
}
```

{{< /details >}}