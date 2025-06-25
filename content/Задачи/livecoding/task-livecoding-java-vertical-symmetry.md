#### 49. Проверка вертикальной симметрии набора точек

**Условие задачи:**  
🔥 Дан массив точек с целочисленными координатами `(x, y)`. Нужно определить, существует ли вертикальная прямая `x = c`, такая что все точки, не лежащие на ней, разбиваются на две группы, симметричные относительно этой прямой.

**Код:**

**Пример:**


```text
Пример:
isVertSym([[0, 0], [0, 1], [1, 1], [2, 2], [3, 1], [4, 1], [4, 0]]) // true
isVertSym([[0, 0], [0, 0], [1, 1], [2, 2], [3, 1], [4, 0], [4, 0]]) // true
isVertSym([[0, 0], [0, 0], [1, 1], [2, 2], [3, 1], [4, 0]]) // false
isVertSym([]) // true
isVertSym([[0, 0]]) // true
isVertSym([[0, 0], [10, 0]]) // true
isVertSym([[0, 0], [11, 1]]) // false
isVertSym([[0, 0], [1, 0], [3, 0]]) // false

```

```java
public class SymmetryChecker {
    public static boolean isVertSym(int[][] points) {
        // TODO
    }
}
````

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Найдите минимальную и максимальную `x` среди всех точек: сумма даст `2*c`.  
💡 Используйте `Map<Point, Integer>` для подсчёта вхождений точек (чтобы учесть дубликаты).  
💡 Для каждой точки `(x, y)` проверяйте, что есть ровно столько же зеркальных `(sum - x, y)`.  
{{< /details >}}

{{< details "Решение" close >}}

```java
import java.util.HashMap;
import java.util.Map;
import java.util.Objects;

public class SymmetryChecker {

    public static boolean isVertSym(int[][] points) {
        if (points == null || points.length <= 1) {
            return true;
        }

        // Находим minX и maxX
        int minX = Integer.MAX_VALUE, maxX = Integer.MIN_VALUE;
        for (int[] p : points) {
            minX = Math.min(minX, p[0]);
            maxX = Math.max(maxX, p[0]);
        }
        int sum = minX + maxX;  // 2 * c

        // Подсчёт вхождений точек
        Map<Point, Integer> countMap = new HashMap<>();
        for (int[] p : points) {
            Point pt = new Point(p[0], p[1]);
            countMap.merge(pt, 1, Integer::sum);
        }

        // Проверка зеркальности
        for (Map.Entry<Point, Integer> e : countMap.entrySet()) {
            Point pt = e.getKey();
            int cnt = e.getValue();
            Point mirror = new Point(sum - pt.x, pt.y);
            if (countMap.getOrDefault(mirror, 0) < cnt) {
                return false;
            }
        }
        return true;
    }

    private static class Point {
        final int x, y;
        Point(int x, int y) { this.x = x; this.y = y; }
        @Override public boolean equals(Object o) {
            if (this == o) return true;
            if (!(o instanceof Point)) return false;
            Point p = (Point) o;
            return x == p.x && y == p.y;
        }
        @Override public int hashCode() {
            return Objects.hash(x, y);
        }
    }
}
```

{{< /details >}}