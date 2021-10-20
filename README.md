# binarySearchTree

Based on [youtube lecture](https://youtu.be/fnqUD4FTE5Q) by [Anton Bely](https://github.com/ToshaBely)

1.  Дерево это граф, который не имеет циклов.

    ![simple tree](./_backstage/img/simple_tree.jpg)

    <details>
    <summary>
    граф
    </summary>

    - Граф — это совокупность объектов и связей между ними. Объекты рассматриваются как вершины или узлы графа, а связи — как дуги, или ребра.
    - Основные элементы графа состоят из вершин графа, его ребер и дуг.
    - Сочетание этих элементов определяют понятия: неориентированный граф, ориентированный граф, смешанный граф.
    - Граф, в котором все линии направленные, называется ориентированным .
    - Две вершины, соединенные дугой или ребром, называются смежными .
    - Граф не имеет цикла если мы не можем вернуться в исходную точку, через какое-либо ребро .

      ![graph](./_backstage/img/graph.png)

    </details>

2.  Дерево может иметь сколько угодгно потомков(узлов), если узел не имеет потомков — он называется листом.
3.  Корень дерева является нулевым урованем. Высота дерева — количество переходов от нулевого уровня до самого отдаленного листа.

## Binary Search Tree (BST)

Бинарное дерево поиска — это дерево (граф без циклов), имеющиее не более двух потомков(бинарное), обладающее дополнительными свойствами: значение левого потомка меньше значения родителя, а значение правого потомка больше значения родителя для каждого узла дерева.

То есть, данные в бинарном дереве поиска хранятся в отсортированном виде, поэтому (search tree, т.е. дерево поиска).

## BST: Remove Item

1. Find item by value

2. Check type of selected item:

   2.1. Leaf - it's cool (do nothing)

   2.2. Has only one child: left or right (set this child node instead of current node)

   2.3. Has both children (find min from the right child and set this value on current position, deleting min node)

## BST: Left Traversing (обход дерева)

![graph](./_backstage/img/traversing_tree.jpg)

<details>
<summary>BST code exumple</summary>
<p>

```javascript
class Node {
  constructor(value) {
    this.value = value;
    this.left = null;
    this.right = null;
  }
}

class BinarySearchTree {
  constructor() {
    this.root = null;
  }

  add(value) {
    this.root = addWithin(this.root, value);

    function addWithin(node, value) {
      if (!node) {
        return new Node(value);
      }

      if (node.value === value) {
        return node;
      }

      if (value < node.value) {
        node.left = addWithin(node.left, value);
      } else {
        node.right = addWithin(node.right, value);
      }

      return node;
    }
  }

  has(value) {
    return searchWithin(this.root, value);

    function searchWithin(node, value) {
      if (!node) {
        return false;
      }

      if (node.value === value) {
        return true;
      }

      return value < node.value
        ? searchWithin(node.left, value)
        : searchWithin(node.right, value);
    }
  }

  remove(value) {
    this.root = removeNode(this.root, value);

    function removeNode(node, value) {
      if (!node) {
        return null;
      }

      if (value < node.value) {
        node.left = removeNode(node.left, value);
        return node;
      } else if (node.value < value) {
        node.right = removeNode(node.right, value);
        return node;
      } else {
        // equal - should remove this item
        if (!node.left && !node.right) {
          // put null instead of item
          return null;
        }

        if (!node.left) {
          // set right child instead of item
          node = node.right;
          return node;
        }

        if (!node.right) {
          // set left child instead of item
          node = node.left;
          return node;
        }

        // both children exists for this item
        let minFromRight = node.right;
        while (minFromRight.left) {
          minFromRight = minFromRight.left;
        }
        node.value = minFromRight.value;

        node.right = removeNode(node.right, minFromRight.value);

        return node;
      }
    }
  }

  min() {
    if (!this.root) {
      return;
    }

    let node = this.root;
    while (node.left) {
      node = node.left;
    }

    return node.value;
  }

  max() {
    if (!this.root) {
      return;
    }

    let node = this.root;
    while (node.right) {
      node = node.right;
    }

    return node.value;
  }

  leftTraverse(cb) {
    doLeft(this.root, cb);

    function doLeft(node, cb) {
      if (node) {
        doLeft(node.left, cb);
        cb(node.value);
        doLeft(node.right, cb);
      }
    }
  }

  rightTraverse(cb) {
    doRight(this.root, cb);

    function doRight(node, cb) {
      if (node) {
        doRight(node.right, cb);
        cb(node.value);
        doRight(node.left, cb);
      }
    }
  }
}

console.log("s01e12 - BST (Binary Search Tree)");

function addItems() {
  console.log("\n  Add Items");
  console.log("add 13, 15, 9, 20, 18, 32, 25");

  bst.add(13);
  bst.add(15);
  bst.add(9);
  bst.add(20);
  bst.add(18);
  bst.add(32);
  bst.add(25);

  //  Should get something like this:
  //    13
  //   /  \
  //  9    15
  //        \
  //         20
  //        /  \
  //       18   32
  //           /
  //          25
}

function getItems() {
  console.log("\n  Get Items");

  console.log("has 10", bst.has(10));
  console.log("has 15", bst.has(15));
  console.log("\n", bst);

  console.log("  Left Traverse:");
  bst.leftTraverse((val) => console.log(val));

  console.log("  Right Traverse:");
  bst.rightTraverse((val) => console.log(val));

  console.log("min:", bst.min());
  console.log("max:", bst.max());
}

function removeItem() {
  console.log("  Remove Item");

  bst.remove(15);
  console.log("remove 15");
  console.log(bst);

  console.log("  Left Traverse:");
  bst.leftTraverse((val) => console.log(val));
}

const bst = new BinarySearchTree();

addItems();
getItems();
removeItem();
```

</p>
</details>
