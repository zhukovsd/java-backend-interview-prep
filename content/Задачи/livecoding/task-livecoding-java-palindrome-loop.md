#### 72. Проверка палиндрома в цикле без методов строк

Написать функцию для проверки палиндрома, обрабатывая строку только в циклах.
Нельзя использовать replace(), trim() и другие методы для обработки строк.
Нужно игнорировать пробелы и проверять только буквы/символы.

---
{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Используйте два указателя: один с начала, другой с конца строки  
💡 Пропускайте пробелы, перемещая указатели внутрь строки  
💡 Сравнивайте символы, игнорируя регистр (Character.toLowerCase())  
💡 Продолжайте до тех пор, пока указатели не встретятся  
💡 Проверяйте, что символы совпадают на каждой итерации  
💡 Обработайте случай, когда строка состоит только из пробелов  
{{< /details >}}

{{< details "Решение" close >}}

```java
public class PalindromeChecker {
    
    public static boolean isPalindrome(String s) {
        if (s == null) return false;
        
        int left = 0;
        int right = s.length() - 1;
        boolean foundValidChars = false;
        
        while (left <= right) {
            // Пропускаем пробелы слева
            while (left <= right && s.charAt(left) == ' ') {
                left++;
            }
            
            // Пропускаем пробелы справа
            while (left <= right && s.charAt(right) == ' ') {
                right--;
            }
            
            // Если после пропуска пробелов не осталось символов
            if (left > right) {
                return foundValidChars;
            }
            
            // Отмечаем, что нашли хотя бы одну пару символов для сравнения
            foundValidChars = true;
            
            // Сравниваем символы (игнорируя регистр)
            char leftChar = Character.toLowerCase(s.charAt(left));
            char rightChar = Character.toLowerCase(s.charAt(right));
            
            if (leftChar != rightChar) {
                return false;
            }
            
            // Перемещаем указатели
            left++;
            right--;
        }
        
        return true;
    }
    
    public static void main(String[] args) {
        // Тестовые случаи из задания
        System.out.println(isPalindrome(" fly me to the moon ")); // false
        System.out.println(isPalindrome("  сел в   озере   березов лес     ")); // true
        
        // Дополнительные тесты
        System.out.println(isPalindrome("а роза упала на лапу азора")); // true
        System.out.println(isPalindrome("hello world")); // false
        System.out.println(isPalindrome("a")); // true
        System.out.println(isPalindrome("   ")); // false (только пробелы)
        System.out.println(isPalindrome("")); // false (пустая строка)
    }
}
```

**Алгоритм по шагам:**

1. **Инициализация указателей** - `left` с начала (0), `right` с конца (длина - 1)
2. **Пропуск пробелов** - двигаем указатели мимо пробелов с обеих сторон
3. **Проверка окончания** - если указатели пересеклись, возвращаем результат
4. **Сравнение символов** - приводим к нижнему регистру и сравниваем
5. **Несовпадение** - возвращаем false при первой ошибке
6. **Движение к центру** - сдвигаем указатели и повторяем

**Особенности решения:**
- Работает с любым количеством пробелов
- Игнорирует регистр букв
- Корректно обрабатывает строки только из пробелов (возвращает false)
- Эффективно использует память (O(1) дополнительной памяти)
- Временная сложность O(n)

**Результаты для тестовых случаев:**
- `" fly me to the moon "` → **false** (не палиндром)
- `" сел в озере березов лес "` → **true** (палиндром)
  {{< /details >}}