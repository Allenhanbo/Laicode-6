### 几种binary tree的类型
1. Balanced binary tree: 对任意node，其左右子树的高度差不超过1 
    - 如果一个balanced binary tree有n个node，的高度为log_2(n)
    - Note：如果没有强调是balanced binary tree，那么树的高度（level）只能认为是O(n)
2. Complete binary tree: 只有最后一层存在null，且所有的null都靠右
    - 如果一个树是Complete binary tree，那它必然也是Balanced tree
    - 可以被存放在array中
3. Binary search tree：对任意node，其左子树的node都比它小，右子树都比它大，没有重复元素（如果有重复，可以counter记录在node里面）
    - 如果in-order打印一个BST，一定是升序排列的有序数组

![lrIV1A.md.png](https://s2.ax1x.com/2020/01/06/lrIV1A.md.png)()

