#### 59. Консольная игра «Крестики-нолики»

**Условие задачи:**  
📌 Написать консольную игру «Крестики-нолики» с произвольным размером поля. Структура:
- `Main` — точка входа, спрашивает размер поля и запускает игру.
- `Game` — логика игры (ходы, проверка победы/ничьи).
- `Desk` — хранит состояние поля, рисует доску.
- `AI` — интерфейс для хода компьютера.
- `Player` — класс для живого игрока (вводит координаты с консоли).
- `IndianAI` — реализация `AI`, делает ход «компьютерно» (случайным выбором).


{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Инкапсулируйте логику хода в интерфейсе `AI` — расширяемость.  
💡 `Desk` отвечает за хранение и проверку победы/ничьи.  
💡 `Game` управляет игровым циклом.  
💡 `Player` читает ввод с консоли, `IndianAI` генерирует случайные ходы.  
{{< /details >}}

{{< details "Решение" close >}}

**Код:**

```java
// Main.java
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        System.out.print("Введите размер поля: ");
        int size = scanner.nextInt();
        Game game = new Game(size, new Player(scanner, 'X'), new IndianAI('O'));
        game.play();
        scanner.close();
    }
}
````

```java
// Desk.java
public class Desk {
    private final char[][] field;
    private final int size;

    public Desk(int size) {
        this.size = size;
        this.field = new char[size][size];
        for (int i = 0; i < size; i++)
            for (int j = 0; j < size; j++)
                field[i][j] = '.';
    }

    public boolean isCellEmpty(int row, int col) {
        return field[row][col] == '.';
    }

    public void setCell(int row, int col, char symbol) {
        field[row][col] = symbol;
    }

    public void print() {
        for (int i = 0; i < size; i++) {
            for (int j = 0; j < size; j++) {
                System.out.print(field[i][j] + " ");
            }
            System.out.println();
        }
    }

    public boolean hasWon(char symbol) {
        // Проверка строк и столбцов
        for (int i = 0; i < size; i++) {
            if (checkLine(symbol, i, 0, 0, 1) || checkLine(symbol, 0, i, 1, 0))
                return true;
        }
        // Диагонали
        if (checkLine(symbol, 0, 0, 1, 1) || checkLine(symbol, 0, size - 1, 1, -1))
            return true;
        return false;
    }

    private boolean checkLine(char symbol, int startRow, int startCol, int dRow, int dCol) {
        for (int k = 0, r = startRow, c = startCol; k < size; k++, r += dRow, c += dCol) {
            if (field[r][c] != symbol) return false;
        }
        return true;
    }

    public boolean isFull() {
        for (int i = 0; i < size; i++)
            for (int j = 0; j < size; j++)
                if (field[i][j] == '.') return false;
        return true;
    }
}
```

```java
// AI.java
public interface AI {
    void makeMove(Desk desk);
}
```

```java
// Player.java
import java.util.Scanner;

public class Player implements AI {
    private final Scanner scanner;
    private final char symbol;

    public Player(Scanner scanner, char symbol) {
        this.scanner = scanner;
        this.symbol = symbol;
    }

    @Override
    public void makeMove(Desk desk) {
        int row, col;
        do {
            System.out.print("Ход игрока '" + symbol + "' (строка и колонка от 1 до " 
                              + deskSize(desk) + "): ");
            row = scanner.nextInt() - 1;
            col = scanner.nextInt() - 1;
        } while (!validMove(desk, row, col));
        desk.setCell(row, col, symbol);
    }

    private boolean validMove(Desk desk, int r, int c) {
        int size = deskSize(desk);
        if (r < 0 || r >= size || c < 0 || c >= size) {
            System.out.println("Неверные координаты.");
            return false;
        }
        if (!desk.isCellEmpty(r, c)) {
            System.out.println("Ячейка занята.");
            return false;
        }
        return true;
    }

    private int deskSize(Desk desk) {
        try {
            java.lang.reflect.Field f = desk.getClass().getDeclaredField("size");
            f.setAccessible(true);
            return f.getInt(desk);
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }
}
```

```java
// IndianAI.java
import java.util.Random;

public class IndianAI implements AI {
    private final char symbol;
    private final Random rand = new Random();

    public IndianAI(char symbol) {
        this.symbol = symbol;
    }

    @Override
    public void makeMove(Desk desk) {
        int size = getSize(desk);
        int r, c;
        do {
            r = rand.nextInt(size);
            c = rand.nextInt(size);
        } while (!desk.isCellEmpty(r, c));
        System.out.println("Ход компьютера '" + symbol + "': " + (r + 1) + " " + (c + 1));
        desk.setCell(r, c, symbol);
    }

    private int getSize(Desk desk) {
        try {
            java.lang.reflect.Field f = desk.getClass().getDeclaredField("size");
            f.setAccessible(true);
            return f.getInt(desk);
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }
}
```

```java
// Game.java
public class Game {
    private final Desk desk;
    private final AI playerX;
    private final AI playerO;

    public Game(int size, AI playerX, AI playerO) {
        this.desk = new Desk(size);
        this.playerX = playerX;
        this.playerO = playerO;
    }

    public void play() {
        desk.print();
        while (true) {
            playerX.makeMove(desk);
            desk.print();
            if (desk.hasWon(getSymbol(playerX))) {
                System.out.println("Победил '" + getSymbol(playerX) + "'!");
                break;
            }
            if (desk.isFull()) {
                System.out.println("Ничья!");
                break;
            }
            playerO.makeMove(desk);
            desk.print();
            if (desk.hasWon(getSymbol(playerO))) {
                System.out.println("Победил '" + getSymbol(playerO) + "'!");
                break;
            }
            if (desk.isFull()) {
                System.out.println("Ничья!");
                break;
            }
        }
    }

    private char getSymbol(AI ai) {
        if (ai instanceof Player) return ((Player) ai).symbol;
        if (ai instanceof IndianAI) return ((IndianAI) ai).symbol;
        return '?';
    }
}
```


{{< /details >}}

```text
Пример запуска:
Введите размер поля: 3
. . .
. . .
. . .
Ход игрока 'X' (строка и колонка от 1 до 3): 1 1
X . .
. . .
. . .
Ход компьютера 'O': 2 2
...
```