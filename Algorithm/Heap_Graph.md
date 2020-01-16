## Heap & Graph Search Algorithm
[Heap](../Notes/Heap.md)

### K Smallest In Unsorted Array
MaxHeap method is an __*online method*__，不需要一次性获得所有数据，而是可以处理数据流
```java
public class Solution {
    public int[] kSmallest(int[] array, int k) {
        //要check 一下k是不是0
        if (array.length == 0 || k == 0) {
            return new int[0];
        }
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(k, new Comparator<Integer>() {//<>不能少
            @Override
            public int compare(Integer o1,Integer o2) {
                //要用equals比较
                if (o1.equals(o2)) {
                    return 0;
                }
                return o1 > o2 ? -1 : 1; //BugHistory: 要记住谁的优先级更高
            }
        });
        for (int i = 0; i < array.length; i++) {
            if (i < k) {
                maxHeap.offer(array[i]);
            } else if (array[i] < maxHeap.peek()) {
                maxHeap.poll();
                maxHeap.offer(array[i]);
            }
        }
        //要返回一下sort好的数组，注意从后往前写入array，因为是maxHeap
        //BugHistory,注意写入的顺序
        int[] res = new int[k];
        for (int i = k - 1; i >= 0; i--) {
            res[i] = maxHeap.poll();
        }
        return res;
    }
}
```

### Get Keys In Binary Tree Layer By Layer
思路：利用一个Queue，不断地expand，generate左右子树  
还要注意，要求分层打印，则需要记录一个当前层的size
```java
public class Solution {
    public List<List<Integer>> layerByLayer(TreeNode root) {
        //外层List
        List<List<Integer>> list = new ArrayList<List<Integer>>();
        if (root == null) {
            return list;
        }
        Queue<TreeNode> queue = new ArrayDeque<>();
        queue.offer(root);
        while (!queue.isEmpty()) {
            //内层List，用来记录每层的数
            List<Integer> curLayer = new ArrayList<>();
            //每次开始expand之前先记录size
            int size = queue.size();
            //开始做这一层的expand，generate
            for (int i = 0; i < size; i++) {
                TreeNode curr = queue.poll();
                curLayer.add(curr.key);//相当于打印出单独的node值
                if (curr.left != null) {
                    queue.offer(curr.left);
                }
                if (curr.right != null) {
                    queue.offer(curr.right);
                }
            }
            list.add(curLayer);//相当于打印分行
        }
        return list;
    }
}
```

### Bipartite
[Bipartite示意图](https://s2.ax1x.com/2020/01/12/loS3SP.png)

```java
public class Solution {
    public boolean isBipartite(List<GraphNode> graph) {
        //map用interface
        Map<GraphNode, Integer> visited = new HashMap<>();
        for (GraphNode node : graph) {
            if (!BFS(node, visited)) {
                return false;
            }
        }
        return true;
    }
    private boolean BFS(GraphNode node, Map<GraphNode, Integer> visited) {
        //如果visited过的node，直接返回true
        if (visited.containsKey(node)) {
            return true;
        }
        //queue用来expand，generate
        Queue<GraphNode> queue = new ArrayDeque<>();
        //放入第一个元素
        queue.offer(node);
        //往map中放node的组，默认为0组
        visited.put(node, 0);
        while (!queue.isEmpty()) {
            //expand顶端元素并存下来
            GraphNode curr = queue.poll();
            //决定不同的组，每个node跟自己的邻居应该都是不同的组
            int curGroup = visited.get(curr);
            int neiGroup = curGroup == 0 ? 1 : 0;
            for (GraphNode nei : curr.neighbors) {
                if (!visited.containsKey(nei)) {
                    visited.put(nei, neiGroup); //map往里放是put
                    queue.offer(nei);// BugHistory：不能忘记map和queue都要往里放
                } else if (visited.get(nei) == curGroup) {
                    return false;
                }
            }
        }
        return true;
    }
}
```


### Check If Binary Tree Is Completed
思路：按层traverse，一旦出现null，就不能再出现null值了
```java
public class Solution {
    public boolean isCompleted(TreeNode root) {
        if (root == null) {
            return true;
        }
        Queue<TreeNode> queue = new ArrayDeque<>();
        //当遇到null，就会变成true
        boolean flag = false;
        queue.offer(root);
        while (!queue.isEmpty()) {
            TreeNode curr = queue.poll();
            /**
             * 此处的if判断逻辑需要小心，如果child不是null，也不能直接
             * offer到queue里面，要先检查flag是不是true
             */ 
            if (curr.left != null) {
                if (flag) {
                    return false;
                } 
                queue.offer(curr.left);
            } else {
                flag = true;
            }
            if (curr.right != null) {
                if (flag) {
                    return false;
                } 
                queue.offer(curr.right);
            } else {
                flag = true;
            }
        }
        return true;
    }
}
```

### Kth Smallest Number In Sorted Matrix
思路：Dijkstra最短路径搜索算法，利用PriorityQueue，实现每次expand，generate都是最短路径

先决条件：所有的路径值要大于0
```java
public class Solution {
    public int kthSmallest(int[][] matrix, int k) {
        int rows = matrix.length;
        int cols = matrix[0].length;
        PriorityQueue<Cell> minHeap = new PriorityQueue<>(k, new Comparator<Cell> (){
            @Override
            public int compare(Cell c1, Cell c2) {
                if (c1.value == c2.value) {
                    return 0;
                } 
                return c1.value < c2.value ? -1 : 1;
            }
        });
        boolean[][] visited = new boolean[rows][cols];
        Cell firstCell = new Cell(0,0,matrix[0][0]);
        visited[0][0] = true;
        minHeap.offer(firstCell);
        for (int i = 0; i < k - 1; i++) { //注意这里的k - 1，因为最小的元素就是matrix[0][0]已经放进去的元素了
            Cell curr = minHeap.poll();
            if (curr.row + 1 < rows && !visited[curr.row + 1][curr.col]) {
                minHeap.offer(new Cell(curr.row + 1, curr.col, matrix[curr.row + 1][curr.col]));
                visited[curr.row + 1][curr.col] = true;
            }
            if (curr.col + 1 < cols && !visited[curr.row][curr.col + 1]) {
                minHeap.offer(new Cell(curr.row, curr.col + 1, matrix[curr.row][curr.col + 1]));
                visited[curr.row][curr.col + 1] = true;
            }
        }
        return minHeap.peek().value;
    }

    static class Cell {
        int row;
        int col;
        int value;

        Cell (int row, int col, int value) {
            this.row = row;
            this.col = col;
            this.value = value;
        }
    }
}
```


