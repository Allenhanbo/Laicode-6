## Binary Tree
[三种Binary Tree的概念](../Notes/BinaryTree.md)
### Tree Traversal
1. pre-order
```java
/**
 * public class TreeNode {
 *   public int key;
 *   public TreeNode left;
 *   public TreeNode right;
 *   public TreeNode(int key) {
 *     this.key = key;
 *   }
 * }
 */
public class Solution {
    public List<Integer> preOrder(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        helper(root, list);
        return list;
    }

    private void helper(TreeNode root, List<Integer> list) {
        if (root == null) {
            return;
        }
        list.add(root.key);
        helper(root.left, list);
        helper(root.right, list);
    }
}
```
2. in-order
```java
public class Solution {
    public List<Integer> inOrder(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        helper(root, list);
        return list;
    }

    private void helper(TreeNode root, List<Integer> list) {
        if (root == null) {
            return;
        }
        helper(root.left, list);
        list.add(root.key);
        helper(root.right, list);
    }
}
```
3. post-order
```java
public class Solution {
    public List<Integer> postOrder(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        helper(root, list);
        return list;
    } 
    private void helper(TreeNode root, List<Integer> list) {
        if (root == null) {
            return;
        }
        helper(root.left, list);
        helper(root.right, list);
        list.add(root.key);
    }
}
```
### Height of Binary Tree
时间空间复杂度分析,best case和worst case不一样
best case是糖葫芦样子的一棵tree，直接到底反弹 time:O(n) space:O(height) = O(n)
worse case是一个完全二叉树，time:O(n), space = O(height) = O(logn)
```java
public class Solution {
    public int findHeight(TreeNode root) {
        //base case: when reach null node, return 0
        if (root == null) {
            return 0;
        }
        int leftHeight = findHeight(root.left);
        int rightHeight = findHeight(root.right);

        return Math.max(leftHeight, rightHeight) + 1;
    }
}
```
### Judge a tree whether a binary tree is symmetric
root不会影响，用helper判断root左子树和右子树是否对称  
```java
public class Solution {
    public boolean isSymmetric(TreeNode root) {
        if (root == null) {
            return true;
        }
        return isSymmetric(root.left, root.right);
    }

    private boolean isSymmetric(TreeNode left, TreeNode right) {
        if (left == null && right == null) {
            return true;
        } else if (left == null || right == null) {
            return false;
        } else if (left.key != right.key) {
            return false;
        }
        return isSymmetric(left.left, right.right) && isSymmetric(left.right, right.left);
    }
}
```

### Check If Binary Tree Is Balanced
这道题有两个解法，第一种解法时间复杂度比较高  
假设这棵树是BST，也就是worse case，isBalanced函数recursion tree就是logn层。对于每层的每一个节点，都要在做一次getHeight recursion，也就是当前node下面一共有多少个node遍历一边，每一层其实时间复杂度还是O(n)，所以最后结果就是O(nlogn)
[![l6TJU0.md.png](https://s2.ax1x.com/2020/01/07/l6TJU0.md.png)]()
```java
public class Solution {
    public boolean isBalanced(TreeNode root) {
        if (root == null) {
            return true;
        }
        int left = findHeight(root.left);
        int right = findHeight(root.right);
        if (Math.abs(left - right) > 1) {
            return false;
        }
        return isBalanced(root.left) && isBalanced(root.right);
    }

    public int findHeight(TreeNode root) {
        //base case: when reach null node, return 0
        if (root == null) {
            return 0;
        }
        int leftHeight = findHeight(root.left);
        int rightHeight = findHeight(root.right);

        return Math.max(leftHeight, rightHeight) + 1;
    }
}
```
**Better solution**
```java
public class Solution {
    public boolean isBalanced(TreeNode root) {
        if (root == null) {
            return true;
        }
        return getHeightOrCheck(root) != -1;
    }

    private int getHeightOrCheck(TreeNode root) {
        //base case
        if (root == null) {
            return 0;
        }
        int leftHeight = getHeightOrCheck(root.left);
        if (leftHeight == -1) {
            return -1;
        }
        int rightHeight = getHeightOrCheck(root.right);
        if (rightHeight == -1) {
            return -1;
        }
        if (Math.abs(leftHeight - rightHeight) > 1) {
            return -1;
        }
        return Math.max(leftHeight, rightHeight) + 1;
    }
}
```


### Tweaked Identical Binary Trees
```java
public class Solution {
    public boolean isTweakedIdentical(TreeNode one, TreeNode two) {
        if (one == null && two == null) {
            return true;
        } else if (one == null || two == null) {
            return false;
        } else if (one.key != two.key) {
            return false;
        }
        return  (isTweakedIdentical(one.left, two.left) && isTweakedIdentical(one.right, two.right))
                ||
                (isTweakedIdentical(one.left, two.right) && isTweakedIdentical(one.right, two.left));
    }
}
```

###  Is Binary Search Tree Or Not
解题思路：根据BST的性质，左子树一定小于root，右子树大于root，这样可以确定一个范围

```java
public class Solution {
    public boolean isBST(TreeNode root) {
        if (root == null) {
            return true;
        }
        return isBST(root, Integer.MIN_VALUE, Integer.MAX_VALUE);
    }
    private boolean isBST(TreeNode root, int min, int max) {
        if (root == null) {
            return true;
        }
        if (root.key <= min || root.key >= max) {
            return false;
        } else {
            return isBST(root.left, min, root.key) && isBST(root.right, root.key, max);
        }
    }
}
```

### Get Keys In Binary Search Tree In Given Range
首先要保证是in-order的traverse的顺序，然后在每一步外面加上剪枝的if判断语句  
```java
public class Solution {
    public List<Integer> getRange(TreeNode root, int min, int max) {
        List<Integer> list = new ArrayList<>();
        getRange(root, min, max, list);
        return list;
    }
    private void getRange(TreeNode root, int min, int max, List<Integer> list) {
        if (root == null) {
            return;
        }
        if (root.key > min) {
            getRange(root.left, min, max, list);
        }
        if (root.key >= min && root.key <= max) {
            list.add(root.key);
        }
        if (root.key < max) {
            getRange(root.right, min, max, list);
        }
    }
}
```·