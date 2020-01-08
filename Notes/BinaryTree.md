### 几种binary tree的类型
1. Balanced binary tree: 对任意node，其左右子树的高度差不超过1 
    - 如果一个balanced binary tree有n个node，的高度为log_2(n)
    - Note：如果没有强调是balanced binary tree，那么树的高度（level）只能认为是O(n)
2. Complete binary tree: 只有最后一层存在null，且所有的null都靠右
    - 如果一个树是Complete binary tree，那它必然也是Balanced tree
    - 可以被存放在array中
3. Binary search tree：对任意node，其左子树的node都比它小，右子树都比它大，没有重复元素（如果有重复，可以counter记录在node里面）
    - 如果in-order打印一个BST，一定是升序排列的有序数组

[![lrIV1A.md.png](https://s2.ax1x.com/2020/01/06/lrIV1A.md.png)]()

### Search in BST
```java
//TreeNode
public class TreeNode {
    public int key;
    public TreeNode left;
    public TreeNode right;
    public TreeNode(int key) {
        this.key = key;
    }
 }
//recursion
//尾递归，最后一步调用自己，其实没有必要压栈浪费空间，可以转变为iteration
public TreeNode search(TreeNode root, int key) {
    //base case
    if (root == null || root.key == key) {
        return root;
    }
    if (root.key < key) {
        return root = search(root.right, key);
    } else {
        return root = search(root.left, key);
    }
}
//iteration
public TreeNode search(TreeNode root, int key) {
    if (root == null) {
        return null;
    }
    while (root != null) {
        if (key < root.key) {
            root = root.left;
        } else if (key == root.key) {
            return root;
        } else {
            root = root.right;
        }
    }
    return null;
}
```

### Insert in BST
```java 
//Iteration
public TreeNode insert(TreeNode root, int target) {
    if (root == null) {
        return new TreeNode(target);
    }
    TreeNode returnRoot = root;//根节点会发生改变，需要记录
    TreeNode pre = null;
    while (root != null) {
        pre = root;
        if (target < root.key) {
            root = root.left;
        } else if (target > root.key) {
            root = root.right;
        } else { //如果已经存在了就不插入，直接返回要
            return returnRoot;
        }
    }
    if (target < pre.key) {
        pre.left = new TreeNode(target);
    } else {
        pre.right = new TreeNode(target);
    }
    return returnRoot;
}

//recursion
public TreeNode insert(TreeNode root, int target) {
    if (root == null) {
        return new TreeNode(target);
    }
    if (target < root.key) {
        root.left = insert(root.left, target);
    } else if (target > root.key) {
        root.right = insert(root.right, target);
    }
    return root;
}
```