### Array Deduplication I
Given a sorted integer array, remove duplicate elements. For each group of elements with the same value keep only one of them. Do this in-place, using the left side of the original array and maintain the relative order of the elements of the array. Return the array after deduplication.

Assumptions

The array is not null
Examples

{1, 2, 2, 3, 3, 3} → {1, 2, 3}
```java
public class Solution {
    public int[] dedup(int[] array) {
        if (array.length <= 1) {
            return array;
        }
        int slow = 0;
        for (int fast = 1; fast < array.length; fast++) {
            if (array[fast] != array[slow]) {
                array[++slow] = array[fast];
            }
        }
        return Arrays.copyOf(array, slow + 1);
    }
}
```

### Array Deduplication II
Given a sorted integer array, remove duplicate elements. For each group of elements with the same value keep at most two of them. Do this in-place, using the left side of the original array and maintain the relative order of the elements of the array. Return the array after deduplication.

Assumptions

The given array is not null
Examples

{1, 2, 2, 3, 3, 3} → {1, 2, 2, 3, 3}

思路：跟slow的前一位比较
```java
public class Solution {
    public int[] dedup(int[] array) {
        if (array.length <= 2) {
            return array;
        }
        int slow = 1;
        for (int fast = 2; fast < array.length; fast++) {
            if (array[fast] != array[slow - 1]) {
                array[++slow] = array[fast];
            }
        }
        return Arrays.copyOf(array, slow + 1);
    }
}
```

### Array Deduplication III
Given a sorted integer array, remove duplicate elements. For each group of elements with the same value do not keep any of them. Do this in-place, using the left side of the original array and and maintain the relative order of the elements of the array. Return the array after deduplication.

Assumptions

The given array is not null
Examples

{1, 2, 2, 3, 3, 3} → {1}
思路：模拟出一个stack出来 
```java
public class Solution {
    public int[] dedup(int[] array) {
        if (array.length <= 1) {
            return array;
        }
        int end = 0;
        for (int i = 1; i < array.length; i++) {
            if (end == -1 || array[i] != array[end]) {
                array[++end] = array[i];
            } else {
                while (i + 1 < array.length && array[i + 1] == array[end]) {
                    i++;
                }
                end--;
            }
        }
        return Arrays.copyOf(array, end + 1);
    }
}
```

### Move 0s To The End II
```java
public class Solution {
    public int[] moveZero(int[] array) {
        if (array.length == 0) {
            return array;
        }
        int end = 0;
        for (int i = 0; i < array.length; i++) {
            if (array[i] != 0) {
                array[end++] = array[i];
            }
        }
        for (int i = end; i < array.length; i++) {
            array[i] = 0;
        }
        return array;
    }
}
```

### Get Keys In Binary Tree Layer By Layer Zig-Zag Order
思路：巧妙的利用deque的两边进出的性质，想像成是两个stack背靠背，每次从一遍的stack拿元素expand，然后从里一遍放进去，要分析清楚奇数偶数层放进去的先后顺序
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
    public List<Integer> zigZag(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        if (root == null) {
            return list;
        }
        Deque<TreeNode> deque = new ArrayDeque<>();
        deque.offerFirst(root);
        int layer = 0;
        while (!deque.isEmpty()) {
            int size = deque.size();
            for (int i = 0; i < size; i++) {
                if (layer % 2 == 0) {
                    TreeNode curr = deque.pollLast();
                    list.add(curr.key);
                    if (curr.right != null) {
                        deque.offerFirst(curr.right);
                    } 
                    if (curr.left != null) {
                        deque.offerFirst(curr.left);
                    }
                } else {
                    TreeNode curr = deque.pollFirst();
                    list.add(curr.key);
                    if (curr.left != null) {
                        deque.offerLast(curr.left);
                    } 
                    if (curr.right != null) {
                        deque.offerLast(curr.right);
                    }
                }
            }
            layer++;
        }
        return list;
    }
}
```

### Lowest Common Ancestor Binary Search Tree I
```java
public class Solution {
    public TreeNode lca(TreeNode root, int p, int q) {
        int small = Math.min(p, q);
        int large = Math.max(p, q);
        while (root != null) {
            if (root.key < small) {
                root = root.right;
            } else if (root.key > large) {
                root = root.left;
            } else {
                return root;
            }
        }
        return null;
    }
}
```

### Lowest Common Ancestor I
```java
public class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode one, TreeNode two) {
        //base case
        if (root == null) {
            return null;
        }
        //如果当前层的root恰好是one或者two，代表找到了，直接返回
        if (root == one || root == two) {
            return root;
        }
        TreeNode left = lowestCommonAncestor(root.left, one, two);
        TreeNode right = lowestCommonAncestor(root.right, one, two);

        if (left != null && right != null) {
            return root;
        }
        return left != null ? left : right;
    }
}
```


### Lowest Common Ancestor II
```java
public class Solution {
    /**
 * public class TreeNodeP {
 *   public int key;
 *   public TreeNodeP left;
 *   public TreeNodeP right;
 *   public TreeNodeP parent;
 *   public TreeNodeP(int key, TreeNodeP parent) {
 *     this.key = key;
 *     this.parent = parent;
 *   }
 * }
 */
    public TreeNodeP lowestCommonAncestor(TreeNodeP one, TreeNodeP two) {
        int left = getNodeHeight(one);
        int right = getNodeHeight(two);

        if (left <= right) {
            return mergeNode(one, two, right - left);
        } else {
            return mergeNode(two, one, left - right);
        }
    }

    private TreeNodeP mergeNode(TreeNodeP high, TreeNodeP low, int diff) {
        while (diff > 0) {
            low = low.parent;
            diff--;
        }
        while (low != high) {
            low = low.parent; 
            high = high.parent;
        }
        return low;
    }

    private int getNodeHeight(TreeNodeP node) {
        int height = 0;
        while (node != null) {
            height++;
            node = node.parent;
        }
        return height;
    }
}
```


### Largest And Smallest
DP的思想，存储一个最大值，一个最小值
```java
public class Solution {
    public int[] largestAndSmallest(int[] array) {
        int[] res = new int[2];
        if (array.length == 0) {
            return res;
        }
        res[0] = array[0];
        res[1] = array[0];
        for (int i = 1; i < array.length; i++) {
            if (array[i] > res[0]) {
                res[0] = array[i];
            }
            if (array[i] < res[1]) {
                res[1] = array[i];
            }
        }
        return res;
    }
}
```

### Largest And Second Largest
assume array is not null and length >= 2
```java
public class Solution {
    public int[] largestAndSecond(int[] array) {
        int[] res = new int[2];
        if (array[0] >= array[1]) {
            res[0] = array[0];
            res[1] = array[1];
        } else {
            res[0] = array[1];
            res[1] = array[0];
        }
        if (array.length == 2) {
            return res;
        }
        for (int i = 2; i < array.length; i++) {
            if (array[i] > res[1] && array[i] < res[0]) {
                res[1] = array[i];
            } else if (array[i] >= res[0]) {
                res[1] = res[0];
                res[0] = array[i];
            }
        }
        return res;
    }
}
```


### Closest Number In Binary Search Tree II

```java

```


### Lowest Common Ancestor IV
跟最基本的找LCA方法差不多。  
几个基本思想：
1. 首先recursion最根本的就是在每一层中记住这一层的root和它的左右子树传上来的内容
2. 如果在当前层，root的左边能找到目标node，右边也能找到，那就应该返回这个root给上一层
3. 如果左右子树中有一个是null，就应该返回不是null的那个node给上一层
4. 一旦找到某一个node是目标node，就无需再走该node的子树，因为它下面的所有目标node的LCA至少也都是这个node
```java 
public class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, List<TreeNode> nodes) {
        Set<TreeNode> set = new HashSet<>(nodes);
        return helper(root, set);
    }

    private TreeNode helper(TreeNode root, Set<TreeNode> set) {
        if (root == null) {
            return root;
        }
        if (set.contains(root)) {
            return root;
        }
        TreeNode l = helper(root.left, set);
        TreeNode r = helper(root.right, set);

        if (l != null && r != null) {
            return root;
        }
        return l != null ? l : r;
    }
}
```

### Lowest Common Ancestor V
思路：LCA核心思想就是去找target node  
这一题中可能存在很多个叉儿，对于每一个root来讲，至少要有两个叉儿找了target，才能返回这一层root，否则只找到一个的话只能返回找到的那个node
```java
/**
 * public class KnaryTreeNode {
 *     int key;
 *     List<KnaryTreeNode> children;
 *     public KnaryTreeNode(int key) {
 *         this.key = key;
 *         this.children = new ArrayList<>();
 *     }
 * }
 */
public class Solution {
    public KnaryTreeNode lowestCommonAncestor(KnaryTreeNode root, KnaryTreeNode a, KnaryTreeNode b) {
        if (root == null) {
            return root;
        }
        if (root == a || root == b) {
            return root;
        }
        KnaryTreeNode found = null;
        for (KnaryTreeNode node : root.children) {
            KnaryTreeNode goToFindTarget = lowestCommonAncestor(node, a, b);
            if (goToFindTarget == null) { //didn't find the target
                continue; //没找到就去下一个叉儿找
            }
            if (found == null) { //到这一步说明找到了，但是要看看是找到的第几个，如果是第一个，那就直接赋值给found
                found = goToFindTarget; 
            } else {//如果found已经有值了，说明这是找到的第二个，那么LCA一定是这一层的root，直接返回root即可
                return root;
            }
        }
        return found; // 否则返回found
    }
}
```

### Lowest Common Ancestor VI

```java
public class Solution {
    public KnaryTreeNode lowestCommonAncestor(KnaryTreeNode root, List<KnaryTreeNode> nodes) {
        Set<KnaryTreeNode> set = new HashSet<>(nodes);
        return helper(root, set);
    }

    private KnaryTreeNode helper(KnaryTreeNode root, Set<KnaryTreeNode> set) {
        if (root == null) {
            return root;
        }
        if (set.contains(root)) {
            return root;
        }
        KnaryTreeNode found = null;
        for (KnaryTreeNode node : root.children) {
            KnaryTreeNode goToFindTarget = helper(node, set);
            if (goToFindTarget == null) { //didn't find the target
                continue; //没找到就去下一个叉儿找
            }
            if (found == null) { //到这一步说明找到了，但是要看看是找到的第几个，如果是第一个，那就直接赋值给found
                found = goToFindTarget; 
            } else {//如果found已经有值了，说明这是找到的第二个，那么LCA一定是这一层的root，直接返回root即可
                return root;
            }
        }
        return found; // 否则返回found
    }
}
```
