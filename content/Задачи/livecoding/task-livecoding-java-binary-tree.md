#### 30. Реализация двоичного дерева

**Условие задачи:**  
🔥 Напишите простую реализацию **двоичного дерева**:

- добавление элементов
    
- обход в **in-order** (сначала левый, потом текущий, потом правый)
    


{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
🌳 **Двоичное дерево поиска** (Binary Search Tree) хранит меньшие значения слева, большие — справа.  
📌 Для вставки используем рекурсивный метод `insertRec`.  
📌 Обход in-order позволяет получить **отсортированную последовательность** значений.  
💡 Используй `System.out.print()` внутри рекурсивного обхода, чтобы увидеть структуру.  
{{< /details >}}

{{< details "Решение" close >}}

```java
public class BinaryTree {

    static class Node {
        int value;
        Node left;
        Node right;

        Node(int value) {
            this.value = value;
        }
    }

    private Node root;

    public void insert(int value) {
        root = insertRec(root, value);
    }

    private Node insertRec(Node root, int value) {
        if (root == null) return new Node(value);

        if (value < root.value) root.left = insertRec(root.left, value);
        else if (value > root.value) root.right = insertRec(root.right, value);

        return root;
    }

    public void inOrderTraversal() {
        inOrderRec(root);
    }

    private void inOrderRec(Node root) {
        if (root != null) {
            inOrderRec(root.left);
            System.out.print(root.value + " ");
            inOrderRec(root.right);
        }
    }

    public static void main(String[] args) {
        BinaryTree tree = new BinaryTree();
        tree.insert(5);
        tree.insert(3);
        tree.insert(7);
        tree.insert(1);
        tree.insert(4);

        System.out.println("In-order traversal:");
        tree.inOrderTraversal();  // Output: 1 3 4 5 7
    }
}
```

📌 **Что ты реализовал:**  
✅ Добавление элементов в бинарное дерево.  
✅ Обход in-order (слева → текущий → справа).  
✅ Рекурсивную структуру дерева.  
✅ Пример работы — можно запустить и проверить.

{{< /details >}}