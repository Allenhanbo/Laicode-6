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
            getRange(root.left,m min, max, list);
        }
        if (root.key >= min && root.key <= max) {
            list.add(root.key);
        }
        if (root.key < max) {
            getRange(root.right, min, max, list);
        }
    }
}
```
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

### Delete in BST
逻辑比较复杂，需要分很多不同的case讨论  

case 1: the deleting node has not child  
case 2: the deleting node has only left child  
case 3: the deleting node has only right child  
case 4: has both child（需要决定谁来上位，并继承原来node的最右子树）  
一般来讲，都是选择该node右子树的最小值来上位，也就是右子树最左下角的node  
case 4.1: the deleting node的右孩子没有左孩子，也就是说右孩子就是最小的那个  
case 4.2: the deleting node的右孩子有左孩子，那就要继续往下找，直到找到没有左孩子的那个，就是最小的 
```java
public class Solution {
    public TreeNode deleteTree(TreeNode root, int key) {
        //base case
        if (root == null) {
            return null;
        }
        //首先要找到该Node，利用search的递归方法
        if (key < root.key) {
            root.left = deleteTree(root.left, key);
            return root;
        } else if (key > root.key) {
            root.right = deleteTree(root.right, key);
            return root;
        }
        //运行到这里的时候，说明上面的if block都不符合，
        //也就说明在这一层stack中，root就是该node
        //然后下面就可以对不同的case进行操作
        //case 1,2,3
        if (root.left == null) {
            return root.right;
        } else if (root.right == null) {
            return root.left;
        }
        //能运行到这一步，说明该node左右孩子都不是null
        //case 4.1
        if (root.right.left == null) {
            //首先要处理要删除node的左子树，把它挂到要继位的那个子树左侧
            root.right.left = root.left; 
            //然后返回要继位的那个node
            return root.right;
        }
        //case 4.2
        //利用一个内部方法，去找到右子树中最小的那个
        TreeNode smallNode = findSmallNode(root.right);
        smallNode.left = root.left;
        smallNode.right = root.right;
        return smallNode;
    }
    private TreeNode findSmallNode(TreeNode cur) {
        TreeNode prev = null; //记录cur的parent node
        while (cur.left != null) {
            prev = cur;
            cur = cur.left;
        }
        //要记得把最小node的右子树接到它父节点左侧
        prev.left = cur.right;
        return cur;
    }
}
```

### Iterative pre-order traversal BST
**pop栈顶，左进去，右进去**
```java
public class Solution {
    public List<Integer> preOrder(TreeNode root) {
        List<Integer> preOrder = new ArrayList<>();
        if (root == null) {
            return preOrder;
        }
        Deque<TreeNode> stack = new ArrayDeque<>();
        stack.offerFirst(root);
        while (!stack.isEmpty()) {
            TreeNode cur = stack.pollFirst();
            preOrder.add(cur.key);
            if (cur.right != null) {
                stack.offerFirst(cur.right);
            } //BugHistory：这两个if一定要分开写，逻辑上是并列的
            if (cur.left != null) {
                stack.offerFirst(cur.left);
            }
        }
        return preOrder;
    }
}
```

### Iterative in-order traversal BST
helper的作用就是提前看前面一个，根据前面一个是什么，来决定下面做什么。  
如果前面一个是不是空，就一直往左下走；走到前面一个是空了，说明左子树走完了，弹栈，打印，然后走到打印出来的右子树，继续上面的操作。  
stack则一直保留着每一层的root，来记录等下回来的时候到哪一个root
```java
public class Solution {
    public List<Integer> inOrder(TreeNode root) {
        List<Integer> inOrder = new ArrayList<>();
        if (root == null) {
            return inOrder;
        }
        Deque<TreeNode> stack = new ArrayDeque<>();
        TreeNode helper = root;
        while (helper != null || !stack.isEmpty()) {
            if (helper != null) {
                stack.offerFirst(helper);
                helper = helper.left;
            } else {
                helper = stack.pollFirst();
                inOrder.add(helper.key);
                helper = helper.right;
            }
        }
        return inOrder;
    }
}
```

### Iterative post-order traversal BST
```java
public class Solution {
    public List<Integer> postOrder(TreeNode root) {
        List<Integer> postOrder = new ArrayList<>();
        if (root == null) {
            return postOrder;
        }
        Deque<TreeNode> stack = new ArrayDeque<>();
        //记录一个prev，初始化为null
        TreeNode prev = null;
        //initial state: stack先把root放进去
        stack.offerFirst(root);

        while (!stack.isEmpty()) {
            //不能直接pop，因为root要最后打印
            TreeNode current = stack.peekFirst(); 
            //case1，需要going down的情况
            if (prev == null || current == prev.left || current == prev.right) {
                if (current.left != null) {
                    stack.offerFirst(current.left);
                } else if (current.right != null) {
                    stack.offerFirst(current.right);
                } else {
                    postOrder.add(stack.pollFirst().key);
                }
            } else if (prev == current.left) { //case2
                if (current.right != null) {
                    stack.offerFirst(current.right);
                } else {
                    postOrder.add(stack.pollFirst().key);
                }
            } else { //case3: prev = current.right
                postOrder.add(stack.pollFirst().key);
            }
            prev = current;
        }
        return postOrder;
    }
}
```