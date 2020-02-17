### Deep Copy Linked List With Random Pointer
思路：利用hashMap对原始的node和它的copy做一个map，可以避免重复copy
```java
/**
 * class RandomListNode {
 *   public int value;
 *   public RandomListNode next;
 *   public RandomListNode random;
 *   public RandomListNode(int value) {
 *     this.value = value;
 *   }
 * }
 */
public class Solution {
    public RandomListNode copy(RandomListNode head) {
        if (head == null) {
            return null;
        }
        Map<RandomListNode, RandomListNode> created = new HashMap<>();
        RandomListNode dummyHead = new RandomListNode(0);
        RandomListNode curr = dummyHead;
        while (head != null) {
            if (!created.containsKey(head)) {
                created.put(head, new RandomListNode(head.value));
            } 
            curr.next = created.get(head);
            if (head.random != null) {
                if (!created.containsKey(head.random)) {
                    created.put(head.random, new RandomListNode(head.random.value));
                }
                curr.next.random = created.get(head.random);
            }
            head = head.next;
            curr = curr.next;
        }
        return dummyHead.next;
    }
}
```

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

### 132. Deep Copy Undirected Graph
```java
/*
* class GraphNode {
*   public int key;
*   public List<GraphNode> neighbors;
*   public GraphNode(int key) {
*     this.key = key;
*     this.neighbors = new ArrayList<GraphNode>();
*   }
* }
*/
public class Solution {
    public List<GraphNode> copy(List<GraphNode> graph) {
        Map<GraphNode, GraphNode> visited = new HashMap<>();
        for (GraphNode node : graph) {
            if (!visited.containsKey(node)) {
                visited.put(node, new GraphNode(node.key));
                DFS(node, visited);
            }
        }
        return new ArrayList<GraphNode>(visited.values());
    }

    private void DFS (GraphNode seed, Map<GraphNode, GraphNode> visited) {
        GraphNode copyNode = visited.get(seed);
        for (GraphNode nei : seed.neighbors) {
            if (!visited.containsKey(nei)) {
                visited.put(nei, new GraphNode(nei.key));
                DFS(nei, visited);
            }
            copyNode.neighbors.add(visited.get(nei));
        }
    }
}
```

### Largest Number Smaller In Binary Search Tree
思路：利用好BST的性质，当前root元素左侧的值都小于root，右侧的都大于root

case 1: if root.key >= target, root goes down the left child  
case 2: if root.key < target, save this key to result; bacause now root.left will all be smaller than root.key, they cannot be the closest one than root.key. So root can only go down right. 
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
    public int largestSmaller(TreeNode root, int target) {
        int result = Integer.MIN_VALUE;
        while (root != null) {
            if (root.key >= target) {
                root = root.left;
            } else {
                result = root.key;
                root = root.right;           
            }
        }
        return result;
    }
}
```


### 2 Sum
```java
public class Solution {
    public boolean existSum(int[] array, int target) {
        // Write your solution here
        Set<Integer> set = new HashSet<>();
        for (Integer num : array) {
            if (set.contains(num)) {
                return true;
            }
            set.add(target - num);
        }
        return false;
    }
}
```

### 2 Sum All Pair I
思路：这题题允许重复，最要注意的地方就是，对于重复的元素，都需要在map中记录下来，不然前面的元素的index会被后面相同的元素覆盖掉。所以利用map中的一个list来记录出现的所有重复的元素的index，一旦找到匹配的值，for循环将所有list中的index都要匹配一遍。
```java
public class Solution {
    public List<List<Integer>> allPairs(int[] array, int target) {
        List<List<Integer>> result = new ArrayList<>();
        Map<Integer, List<Integer>> map = new HashMap<>();
        for (int i = 0; i < array.length; i++) {
            List<Integer> count = map.get(target - array[i]);
            if (count != null) {
                for (Integer index : count) {
                    result.add(Arrays.asList(index, i));
                }
            }
            //put current number into map
            if (!map.containsKey(array[i])) {
                map.put(array[i], new ArrayList<Integer>());
            }
            map.get(array[i]).add(i);
        }
        return result;
    }
}
```


### 2 Sum All Pair II
思路：这题的方法很多，利用hashtable来去重复。也可以利用一个map来记录存进去的值和它出现的次数。
```java
public class Solution {
    public List<List<Integer>> allPairs(int[] array, int target) {
        List<List<Integer>> result = new ArrayList<>();
        Map<Integer, Integer> map = new HashMap<>();
        Set<Integer> deduplicate = new HashSet<>();
        for (Integer num : array) {
            if (!map.containsKey(num)) {
                map.put(target - num, num);
            } else if (!deduplicate.contains(num)) {
                result.add(Arrays.asList(map.get(num), num));
                deduplicate.add(num);
            }
        }
        return result;
    }
}
```

### Merge K Sorted Array
思路：利用一个额外的内部class entry来同时记录某一个值的value，x，y坐标。

其他的思路都跟下面的merge linked list一样，利用minHeap实现。一些细节部分需要注意，尤其是二维array坐标要弄清楚
```java
public class Solution {
    public int[] merge(int[][] arrayOfArrays) {
        PriorityQueue<Entry> minHeap = new PriorityQueue<>(new Comparator<Entry>(){
            @Override
            public int compare(Entry e1, Entry e2) {
                if (e1.value == e2.value) {
                    return 0;
                }
                return e1.value < e2.value ? -1 : 1;
            }
        }); 
        //下面的for loop主要作用是1. 把每一行的第一个元素放进minHeap，同时算一下一共有多少个数在matrix中
        int length = 0;
        for (int i = 0; i < arrayOfArrays.length; i++) {
            int[] array = arrayOfArrays[i];
            length += array.length;
            if (array.length != 0) {
                minHeap.offer(new Entry(array[0], i, 0));
            }
        }
        int[] result = new int[length];
        int count = 0;
        while (minHeap.size() > 0) {
            Entry curr = minHeap.poll();
            result[count++] = curr.value;
            if (curr.y + 1 < arrayOfArrays[curr.x].length) {  // 看一下这个数后面还有没有数了
                curr.y++;
                curr.value = arrayOfArrays[curr.x][curr.y];
                minHeap.offer(curr);
            }
        }
        return result;
    }

    static class Entry {
        int value;
        int x; 
        int y;
        Entry (int value, int x, int y) {
            this.value = value;
            this.x = x; 
            this.y = y;
        }
    }
}

```

### Merge K Sorted Lists
思路：把K个list的头节点放进minHeap，dummyhead每次接上最小的一个。然后拿出去的那个list的下一个，如果不是null的话，就放进minHeap

要注意heap的时间复杂度分析, 每次insert一个元素都是O(logn)的时间复杂度，最后每个元素都会被insert进heap中一次，所以是**O(nlogn)**
```java
/**
 * class ListNode {
 *   public int value;
 *   public ListNode next;
 *   public ListNode(int value) {
 *     this.value = value;
 *     next = null;
 *   }
 * }
 */
public class Solution {
    public ListNode merge(List<ListNode> listOfLists) {
        PriorityQueue<ListNode> minHeap = new PriorityQueue<>(new Comparator<ListNode>(){
            @Override
            public int compare(ListNode n1, ListNode n2) {
                if (n1.value == n2.value) {
                    return 0;
                }
                return n1.value < n2.value ? -1 : 1;
            }
        });
        for (ListNode node : listOfLists) { // O(klogk)
            if (node != null) {
                minHeap.offer(node);
            }
        }
        ListNode dummyHead = new ListNode(0);
        ListNode curr = dummyHead;
        while (minHeap.size() > 0) {
            ListNode node = minHeap.poll();
            curr.next = node;
            if (node.next != null) {
                minHeap.offer(node.next); //O(nlogn)
            }
            curr = curr.next;
        }
        return dummyHead.next;
    }
}
```


### Get Count Array
[解题思路：利用merge sort的性质巧妙解决](https://segmentfault.com/a/1190000012866241)  
time:O(nlogn)  
space:O(1)
```java
public class Solution {
    public int[] countArray(int[] array) {
        int[] indexArray = toIndexArray(array);
        int[] countArray = new int[array.length];
        int[] helper = new int[array.length];
        mergeSort(array, indexArray, countArray, helper, 0, array.length  - 1);
        return countArray;
    }
    private int[] toIndexArray(int[] array) {
        int[] indexArray = new int[array.length];
        for (int i = 0; i < array.length; i++) {
            indexArray[i] = i;
        }
        return indexArray;
    }

    private void mergeSort(int[] array, int[] indexArray, int[] countArray, int[] helper, int left, int right) {
        if (left >= right) {
            return;
        }

        int mid = left + (right - left) / 2;
        mergeSort(array, indexArray, countArray, helper, left, mid);
        mergeSort(array, indexArray, countArray, helper, mid + 1, right);
        merge(array, indexArray, countArray, helper, left, mid, right);
    }

    private void merge (int[] array, int[] indexArray, int[] countArray, int[] helper, int left, int mid, int right) {
         copyArray(indexArray, helper, left, right);
         int l = left; 
         int r = mid + 1;
         int curr = left;
         while (l <= mid) {
             if(r > right || array[helper[l]] <= array[helper[r]]) {
                 countArray[helper[l]] += r - (mid + 1);
                 indexArray[curr++] = helper[l++];
             } else {
                 indexArray[curr++] = helper[r++];
             }
         }
    }

    private void copyArray (int[] indexArray, int[] helper, int left, int right) {
        for (int i = left; i <= right; i++) {
            helper[i] = indexArray[i];
        }
    }
}
```


### 3 Sum
思路：利用了双指针，并且要将原数组排序，才能去除重复元素造成的影响。 

最外层的for loop，确定下来第一个值，然后while循环里面做的其实就是two sum， 利用首尾指针相加。
在while循环里面，left指针如果在匹配上之后，left++，然后要check是否还是一样的值，因为第一个第二个数都确定的情况下，3sum的组合就已经是唯一的了，再出现相同的值就是重复了。
```java
public class Solution {
    public List<List<Integer>> allTriples(int[] array, int target) {
        List<List<Integer>> result = new ArrayList();
        Arrays.sort(array);
        for (int i = 0; i < array.length - 2; i++) {
            //首先去除元素的重复
            if (i > 0 && array[i] == array[i - 1]) {
                continue;
            }
            int remain = target - array[i];
            //确定第一个元素后，在其后面的所有元素中做 2 sum
            int left = i + 1;
            int right = array.length - 1;
            while (left < right) {
                int twoSum = array[left] + array[right];
                if (twoSum == remain) {
                    result.add(Arrays.asList(array[i], array[left], array[right]));
                    left++;
                    while (left < right && array[left] == array[left - 1]) { //这个while不能少
                        left++;
                    }
                    right--;
                } else if (twoSum < remain) {
                    left++;
                } else {
                    right--;
                }
            }
        }
        return result;
    }
}
```


### 4 Sum
思路：两两pair，看成是pair的2sum
```java
public class Solution {
    public boolean exist(int[] array, int target) {
        Map<Integer, Pair> map = new HashMap<>();
        for (int i = 1; i < array.length; i++) {
            for (int j = 0; j < i; j++) {
                int sumPair = array[i] + array[j];
                //下面的条件需要注意一下，要确保map中匹配上的pair的右index小于当前pair的左index，确保不会重复
                if (map.containsKey(target - sumPair) && map.get(target - sumPair).right < j) {
                    return true;
                }
                if (!map.containsKey(sumPair)) {
                    map.put(sumPair, new Pair(j ,i));
                }
            }
        }
        return false;
    }

    class Pair {
        int left;
        int right; 

        Pair (int left, int right) {
            this.left = left; 
            this.right = right;
        }
    }
}

```
