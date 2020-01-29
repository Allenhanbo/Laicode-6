### Closest Number In Binary Search Tree
binary search tree的基本方法，但是在查找过程中，记录一个离target最近的node  
assume root is not null and there is no duplicate keys in the tree
```java
public class Solution {
    public int closest(TreeNode root, int target) {
        int result = root.key;
        while (root != null) {
            if (root.key == target) {
                return root.key;
            } else {
                if (Math.abs(root.key - target) < Math.abs(result - target)) {
                    result = root.key;
                }
                if (root.key < target) {
                    root = root.right;
                } else {
                    root = root.left;
                }
            }
        }
        return result;
    }
}
```

