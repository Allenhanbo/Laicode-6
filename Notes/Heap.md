Heap(priority queue): 维护一个变化的最优值（最大or最小），MaxHeap or MinHeap   

### Binary Heap
利用一个complete tree实现heap，让任意节点要小于(等于)它的后裔  
可以用一个array去存储  
1. index of left child = myIndex * 2 + 1
2. index of right child = myIndex * 2 + 2
3. index of parent = (myIndex - 1) / 2

**Operations**:  
1. get/top: 获得堆顶元素 O(1)
2. insert: 向堆中插入一个元素: 先满足完全树性质，再向上swap，直到满足堆序性O(logn)
3. update: 将新元素提升使其满足堆的性质O(logn)
4. poll: 删除堆顶元素 O(logn)
5. heapify: 使得一个unsorted array变成一个堆 O(n)

**Heapify()**   
- 从底到上，从右往左，对每一个非叶节点做heapify

- 要注意heapify其实是对一个array去操作，心中有一棵树

- 如何找到最后一个非叶节点——其实就是最后一个节点的父节点。
    - 最后一个非叶节点的index计算公式：[(n - 1) - 1] / 2 = n/2 - 1
    - 给一个array.length == n，需要做heapify的节点index是[0, n/2 - 1]

Heapify的时间复杂度分析：
每层向下看了几层？最终是一个差比数列求和，利用错位相减法，算出来时间复杂度是O(n)。主要原因是，在tree结构中，越靠下层的值越多，大多数元素都只要向下比较一层或者很少的层数。

```java
// Heap implementation
public class MinHeap {
    private int[] array;
    private int size;

    private void heapify() {
        //int lastNode = array.length / 2 - 1; => size / 2 - 1
        for (int i = size; i >= 0; i--) {
            percolateDown(i);
        }
    }

    private void percolateUp(int index) {
        while (index > 0) {
            int parentIndex = (index - 1) / 2;
            if (array[parentIndex] > array[index]) {
                swap(array, parentIndex, index);
            } else {
                break;
            }
            index = parentIndex;
        }
    }

    private void percolateDown(int index) {
        while (index <= size / 2 - 1) {
            int leftChildIndex = index * 2 + 1;
            int rightChildIndex = index * 2 + 2;
            int candidate = leftChildIndex;
            if (rightChildIndex <= size - 1 && array[leftChildIndex] >= array[rightChildIndex]) {
                candidate = rightChildIndex;
            }
            if (array[index] > array[candidate]) {
                swap(array, index, candidate);
            } else {
                break;
            }
            index = candidate;
        }
    }

    private void swap (int[] array, int a, int b) {
        int temp = array[a];
        array[a] = array[b];
        array[b] = temp;
    }

}

```