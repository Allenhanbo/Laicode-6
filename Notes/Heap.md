Heap(priority queue): 维护一个变化的最优值（最大or最小），MaxHeap or MinHeap   

### Binary Heap
利用一个complete tree实现heap，让任意节点要小于(等于)它的后裔  
可以用一个array去存储  
1. index of left child = myIndex * 2 + 1
2. index of right child = myIndex * 2 + 2
3. index of parent = (myIndex - 1) / 2

Operations:  
1. get/top: 获得堆顶元素 O(1)
2. insert: 向堆中插入一个元素: 先满足完全树性质，再向上swap，直到满足堆序性O(logn)
3. update: 将新元素提升使其满足堆的性质O(logn)
4. poll: 删除堆顶元素 O(logn)
5. heapify: 使得一个unsorted array变成一个堆 O(n)