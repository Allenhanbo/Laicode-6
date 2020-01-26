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