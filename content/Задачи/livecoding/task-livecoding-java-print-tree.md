#### 66. Вывод дерева в виде иерархии

Дан код. Написать код в методе printNode(), чтобы вывести дерево в нужном виде

```java
package test.test4;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.stream.Collectors;

/**
* Описание: Дан массив Node со ссылкой на родителя (parentId), необходимо
* построить дерево
*/

class Node {

    private final Integer id;
    private final Integer parentId;
    private final List<Node> childNode = new ArrayList<>();

    public Node(Integer id, Integer parentId) {
        this.id = id;
        this.parentId = parentId;
    }

    public Integer getId() {
        return id;
    }

    public Integer getParentId() {
        return parentId;
    }

    public List<Node> getChaildNode() {
        return childNode;
    }

}

public class Test4 {

    public static void main(String[] args) {
        Test4 program = new Test4();
        var nodes = List.of(new Node(1, null),
                new Node(2, 1),
                new Node(3, 1),
                new Node(4, 3),
                new Node(5, 3),
                new Node(6, 5));
        program.linkNodes(nodes);
        System.out.println(program.printNode(nodes.get(0), """", """"));
    }

    public void linkNodes(List<Node> nodes) {
        var nodesMap = nodes.stream()
                .filter(t -> t.getParentId() != null)
                .collect(Collectors.groupingBy(Node::getParentId));
        nodes.forEach(t -> t.getChaildNode().addAll(nodesMap.getOrDefault(t.getId(), Collections.emptyList())));
    }

    /**
    * Такое дерево в итоге должно получиться
    1
    ├── 2
    └── 3
        ├── 4
        └── 5
            └── 6
    */

    public String printNode(Node node, String prefix, String childrenPrefix) {
        
        }
}

```

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 В методе `printNode` нужно добавлять в строку текущий узел (`prefix + id`).  
💡 Для каждого ребёнка вычислять новый префикс:

- Если это последний элемент → `└──` и смещение `" "`.

- Иначе → `├──` и смещение `"│ "`.  
  💡 Рекурсивно вызывать `printNode` для дочерних узлов.  
  {{< /details >}}


{{< details "Решение" close >}}

```java
public String printNode(Node node, String prefix, String childrenPrefix) {
    StringBuilder sb = new StringBuilder();
    sb.append(prefix).append(node.getId()).append("\n");

    List<Node> children = node.getChaildNode();
    for (int i = 0; i < children.size(); i++) {
        Node child = children.get(i);
        boolean isLast = (i == children.size() - 1);
        String newPrefix = childrenPrefix + (isLast ? "└── " : "├── ");
        String newChildrenPrefix = childrenPrefix + (isLast ? "    " : "│   ");
        sb.append(printNode(child, newPrefix, newChildrenPrefix));
    }
    return sb.toString();
}

```

{{< /details >}}