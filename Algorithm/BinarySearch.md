### Class 01 binary search

Thetha(n): tight bound  
Omega(n): lower bound  

#### 1.1 Classical binary search
sorted array  
left, mid, right pointers  
**debug可以用一个元素试试**  
``` java
    while(left <= right) //等于号不能少
    left = mid +- 1;
    right = mid +-1;
```

#### 1.2 Classical binary search in 2D array  
思路：2D ==> 1D array  
要把1D中的index转化成2D中的行列坐标  
**r = index / col;**  
**c = index % col;**  

> #### 保证2个原则  
> 1. 每一轮搜索空间要减少
> 2. 目标值一定不在被抛弃的那一边


#### 1.3举一反一：closest element to target
思路：找到target的左右边界  
```java
    //因为无法当场确定a[mid]是不是最优解，所以不能直接抛弃
    while(left < right - 1)
    left = mid; 
    right = mid; 
```

#### 1.4 first target
有重复元素，返回第一次出现的target的index  
array = {4 5 5 5 5 5 5 5 5}  
思路：第一次出现的一定靠left指针，右侧指针不能跳
；最后判断一定要先判断左，再判断右


#### 1.5 last target
思路：跟1.4相反，首先最后的元素一定靠右，最后判断时先右后左

#### 1.6举一反二 Closest k elements
找到离target最近的k个元素  
思路1：找到target左右边界，然后左右边轮流比较，两边开花(for循环k次)
time = O(log(n) + k)
思路2: 如果k很大，time就很大。（详见1.9）

#### 1.7 Smallest element that is larger than target
找到比target大的数中最小的值  
思路：类似于找first element
因为比target大的最小值一定是靠右的，所有右侧指针比较特殊，最后判断先左后右。


#### 1.8举一反三 K-th smallest in two sorted arrays
从两个有序数组中找到最小的k个值/第k小的值  
思路：每次比较两个array中第k/2个值的大小

#### 1.9--1.6的第二种解法：Closest k element
思路：可以把array变成两个arrays，找到target的左右边界，然后左侧的array是左边的元素到target的距离（与target的差值），右侧同理。
后半部分就是1.8的solution

#### 1.10 binary search with unknown size
知道target，不知道dictionary的size
思路：2倍jump，直到出界，然后做binary search  
follow up：2倍好还是10倍好？  
定量分析和定性分析时间复杂度。



