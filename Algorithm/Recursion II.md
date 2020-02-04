### Spiral Order Traverse I
思路：主要就是一圈圈打印，四个for loop起点和终点要想清楚，需要记录起始位置offset和当前打印的matrix的长度size
```java
public class Solution {
    public List<Integer> spiral(int[][] matrix) {
        List<Integer> list = new ArrayList<>();
        recursiveTraverse(matrix, 0, matrix.length, list);
        return list;
    }

    private void recursiveTraverse(int[][] matrix, int offset, int size, List<Integer> list) {
        if (size == 0) {
            return;
        }
        if (size == 1) {
            list.add(matrix[offset][offset]);
            return;
        }
        for (int i = 0; i < size - 1; i++) {
            list.add(matrix[offset][offset + i]);
        }
        for (int i = 0; i < size - 1; i++) {
            list.add(matrix[offset + i][offset + size - 1]);
        }
        for (int i = size - 1; i > 0; i--) {
            list.add(matrix[offset + size - 1][offset + i]);
        }
        for (int i = size - 1; i > 0; i--) {
            list.add(matrix[offset + i][offset]);
        }
        recursiveTraverse(matrix, offset + 1, size - 2, list);
    }
}
```

### N Queens
思路：对于每一种可能的组合，都是一个array，index表示matrix的row，存储的值表示matrix的col，要符合条件，每一个col的值一定只能出现一次。此外，还需要用三个额外的array去记录col，上下斜对角线的值。

col + row就是向下斜对角线的值  
col - row + n - 1 是从下向上对角线的值
```
                14
                13
                12
                11
                10
                9
                8
                X X X X X X X X
                X X X X X X X X
                X X X X X X X X
                X X X X X X X X
                X X X X X X X X
                X X X X X X X X
                X X X X X X X X
                X X X X X X X X
                8 
                9
                10
                11
                12
                13
                14
```

```java
public class Solution {
    public List<List<Integer>> nqueens(int n) {
        List<List<Integer>> result = new ArrayList<>();
        boolean[] column =  new boolean[2 * n - 1];
        boolean[] downDiag =  new boolean[2 * n - 1];
        boolean[] upDiag =  new boolean[2 * n - 1];
        
        int[] curr = new int[n];
        helper(n, 0, curr, result, column, downDiag, upDiag);
        return result;
    }

    private void helper(int n, int row, int[] curr, List<List<Integer>> result, boolean[] column, boolean[] downDiag, boolean[] upDiag) {
        //base case
        if (row == n) {
            result.add(toList(curr));
        }
        for (int i = 0; i < n; i++) {
            if (valid(n, row, i, column, downDiag, upDiag)) {
                mark(n, row, i, column, downDiag, upDiag);
                curr[row] = i;
                helper(n, row + 1, curr, result, column, downDiag, upDiag);
                unMark(n, row, i, column, downDiag, upDiag);
            }
        }
    }

    private boolean valid(int n, int row, int col, boolean[] column, boolean[] downDiag, boolean[] upDiag) {
        return !column[col] && !downDiag[col + row] && !upDiag[col - row + n - 1];
    }

    private void mark(int n, int row, int col, boolean[] column, boolean[] downDiag, boolean[] upDiag) {
        column[col] = true;
        downDiag[col + row] = true;
        upDiag[col - row + n - 1] = true;
    }

    private void unMark(int n, int row, int col, boolean[] column, boolean[] downDiag, boolean[] upDiag) {
        column[col] = false;
        downDiag[col + row] = false;
        upDiag[col - row + n - 1] = false;
    }

    private List<Integer> toList(int[] array) {
        List<Integer> list = new ArrayList<>();
        for (Integer num : array) {
            list.add(num);
        }
        return list;
    }
}
```

### Reverse Linked List In Pairs
把后面虚线框起来，利用相同的逻辑去解决
![1YZIzj.md.png](https://s2.ax1x.com/2020/02/02/1YZIzj.md.png)
```java
public class Solution {
    public ListNode reverseInPairs(ListNode head) {
        //base case
        if (head == null || head.next == null) {
            return head;
        }
        ListNode next = head.next;
        head.next = reverseInPairs(head.next.next);
        next.next = head;
        return next;
    }
}
```

### String Abbreviation Matching
思路：遇到字母就比较是都相同，遇到数字就算出数字是多少，让input的指针直接跳过相应数字长度  
要注意recursion的base case。当两者的指针同时等于两个的长度的时候，也就是同时到达末尾的时候才能返回true，如果只有一个到末尾或者超过，都要返回false

```java
public class Solution {
    public boolean match(String input, String pattern) {
        return match(input, pattern, 0, 0);
    }

    private boolean match(String input, String pattern, int i, int j) {
        if (i == input.length() && j == pattern.length()) {
            return true;
        }
        if (i >= input.length() || j >= pattern.length()) {
            return false;
        }
        //case 1: not number
        if (pattern.charAt(j) < '0' || pattern.charAt(j) > '9') {
            if (input.charAt(i) == pattern.charAt(j)) {
                return match(input, pattern, i + 1, j + 1);
            }
            return false;
        }
        //case 2: is number
        int count = 0;
        while (j < pattern.length() && pattern.charAt(j) >= '0' && pattern.charAt(j) <= '9') {
            count = count * 10 + pattern.charAt(j) - '0';
            j++;
        }
        return match(input, pattern, i + count, j);
    }
}

```

### Store Number Of Nodes In Left Subtree
思路：  
1. 问左右子树要什么？
2. 当前层做什么？
3. 返回给父节点什么？

首先当前层需要做的事情是计算当前层的总和

问左右子树要所有node

返回给父节点左右node和 + 1

```java
/**
 * public class TreeNodeLeft {
 *   public int key;
 *   public TreeNodeLeft left;
 *   public TreeNodeLeft right;
 *   public int numNodesLeft;
 *   public TreeNodeLeft(int key) {
 *     this.key = key;
 *   }
 * }
 */
public class Solution {
    public void numNodesLeft(TreeNodeLeft root) {
        helper(root);
    }

    private int helper(TreeNodeLeft root) {
        if (root == null) {
            return 0;
        }
        int leftNode = helper(root.left);
        int rightNode = helper(root.right);
        root.numNodesLeft = leftNode;
        return leftNode + rightNode + 1;
    }
}
```