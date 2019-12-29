### Class 01 binary search

#### 1.1 Classical binary search
**debug可以用一个元素试试**  

Given a target integer T and an integer array A *sorted* in _ascending order_, find the index i such that A[i] == T or return -1 if there is no such index.

Assumptions

There can be duplicate elements in the array, and you can return any of the indices i such that A[i] == T.
Examples

- A = {1, 2, 3, 4, 5}, T = 3, return 2
- A = {1, 2, 3, 4, 5}, T = 6, return -1
- A = {1, 2, 2, 2, 3, 4}, T = 2, return 1 or 2 or 3
Corner Cases

What if A is null or A is of zero length? We should return -1 in this case.

思路：经典二分查找
1. 注意corner case
2. 经典二分查找是递增有序数组
3. 双指针，收尾指，取中间值比大小，根据大小结果调整左右指针位置
4. left, right都是存储的index

**time: log(n)**

```java
public class Solution{
    public int binarySearch(int[] array, int target){
        if (array == null || array.length == 0){
            return -1;
        }
        int left = 0;
        int right = array.length - 1;
        int mid;
        while (left <= right) {
            mid = right + (left - right) / 2;
            if (array[mid] == target){
                return mid;
            } else if(array[mid] < target){
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return -1;
    }
}
```



#### 1.2 Classical binary search in 2D array  
思路：2D ==> 1D array  
要把1D中的index转化成2D中的行列坐标  
**r = index / col;**  
**c = index % col;**  

> #### 保证2个原则  
> 1. 每一轮搜索空间要减少
> 2. 目标值一定不在被抛弃的那一边

``` java
/*
    Assumptions: 
    1. Ascending order
    2. No duplicate number
*/
public class Solution {
    public int[] search(int[][] matrix, int target) {
        if (matrix == null) {
            return new int[]{-1, -1};
        }
        int rows = matrix.length;
        int cols = matrix[0].length;
        int left = 0;
        int right = rows * cols - 1;
        int mid;
        while (left <= right) {
            mid = left + (right - left) / 2;
            int row = mid / cols;
            int col = mid % cols;
            if (matrix[row][col] == target) {
                return new int[]{row, col};
            } else if (matrix[row][col] < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return new int[]{-1, -1};
    }
}
```
**Conclusion**: 低级编译错误过多，注意变量的定义，名称以及返回值的类型。  

#### 1.3举一反一：closest element to target
思路：找到target的左右边界  
1. clarification: sorted, ascending, descending
2. assumptions:
    corner case: 
    Multiple solutions(any)/null/empty

Given a target integer T and an integer array A sorted in ascending order, find the index i in A such that A[i] is closest to T.

Assumptions

There can be duplicate elements in the array, and we can return any of the indices with same value.

```java
    //因为无法当场确定a[mid]是不是最优解，所以不能直接抛弃
public class Solution {
    public int closest(int[] array, int target) {
        if (array == null || array.length == 0) {
            return -1;
        }
        int left = 0;
        int right = array.length - 1;
        int mid;
        while (left < right - 1) {
            mid = left + (right - left) / 2;
            if (array[mid] == target) {
                return mid;
            } else if (array[mid] < target) {
                left = mid;
            } else {
                right = mid;
            }
        }
        if (Math.abs(array[left] - target) < Math.abs(array[right] - target)) {
            return left;
        } 
        return right;
    }
}
```

#### 1.4 first target
有重复元素，返回第一次出现的target的index  
array = {4 5 5 5 5 5 5 5 5}  
思路：第一次出现的一定靠left指针，右侧指针不能跳
；最后判断一定要先判断左，再判断右

Given a target integer T and an integer array A sorted in ascending order, find the index of the first occurrence of T in A or return -1 if there is no such index.  

Assuption: 
1. Ascending order
```java
public class Solution {
    public int firstOccur(int[] array, int target) {
        if (array == null || array.length == 0) {
            return -1;
        }
        int left = 0;
        int right = array.length - 1;
        int mid;
        while (left < right - 1) {
            mid = left + (right - left) / 2;
            if (array[mid] == target) {
                right = mid;
            } else if (array[mid] < target) {
                left = mid + 1; // left = mid is right too
            } else {
                right = mid - 1;
            }
        }
        if (array[left] == target) {
            return left;
        } else if (array[right] == target) {
            return right;
        } else {
            return -1;
        }
    } 
}
```


#### 1.5 last target
思路：跟1.4相反，首先最后的元素一定靠右，最后判断时先右后左
```java
public class Solution {
    public int lastOccur(int[] array, int target) {
        if (array == null || array.length == 0) {
            return -1;
        }
        int left = 0;
        int right = array.length - 1;
        int mid;
        while (left < right - 1) {
            mid = left + (right - left) / 2;
            if (array[mid] <= target) {
                left = mid;
            } else {
                right = mid - 1;
            }
        }
        if (array[right] == target) {
            return right;
        } else if (array[left] == target) {
            return left;
        } else {
            return -1;
        }
    } 
}
```


#### 1.6举一反二 Closest k elements

找到离target最近的k个元素  
思路1：找到target左右边界，然后左右边轮流比较，两边开花(for循环k次)
time = O(log(n) + k)
思路2: 如果k很大，time就很大。（详见1.9）

用内部的方法找到比target小或者相等的最大值，然后以该值和其右侧的值为起点，左右开花找到k个最接近的值

**Question:**  
Given a target integer T, a non-negative integer K and an integer array A sorted in ascending order, find the K closest numbers to T in A.  

**Assumptions**  
- A is not null
- K is guranteed to be >= 0 and K is guranteed to be <= A.length  
**Return**  
A size K integer array containing the K closest numbers(not indices) in A, sorted in ascending order by the difference between the number and T. 
```java
//assum array is not null
public class Solution {
    public int[] kClosest(int[] array, int target, int k) {
        if (array.length == 0 || k == 0) {
            return new int[]{}; 
        }
        int[] res = new int[k];
        int left = findLargestSmallerIndex(array, target);
        int right = left + 1;
        for (int i = 0; i < k; i++) {
            if (right >= array.length || left >= 0 && target - array[left] <= array[right] - target) {
                res[i] = array[left--];
            } else {
                res[i] = array[right++];
            }
        }
        return res;
    }

    private int findLargestSmallerIndex(int[] array, int target) {
        int left = 0;
        int right = array.length - 1;
        int mid;
        while (left < right - 1)  {
            mid = left + (right - left) / 2;
            if (array[mid] <= target) {
                left = mid;
            } else {
                right = mid;
            }
        }
        if (array[right] <= target) {
            return right;
        } else if (array[left] <= target){
            return left;
        } else {
            return -1;
        }   
    }
}
```



#### 1.7 Smallest element that is larger than target
找到比target大的数中最小的值  
思路：类似于找first element
因为比target大的最小值一定是靠右的，所有右侧指针比较特殊，最后判断先左后右。

```java
public class Solution {
    public int smallestElementLargerThanTarget(int[] array, int target) {
        if (array == null || array.length == 0) {
            return -1;
        }
        int left = 0;
        int right = array.length - 1;
        int mid;
        while (left < right - 1) {
            mid = left + (right - left) / 2;
            if (array[mid] <= target) {
                left = mid;
            } else {
                right = mid;
            }
        }
        int largestSmallerOrEqual = -1;
        if (array[right] <= target ) {
            largestSmallerOrEqual = right;
        } else if (array[left] <= target ) {
            largestSmallerOrEqual = left;
        } 
        
        if (largestSmallerOrEqual + 1 <= array.length - 1) {
            return largestSmallerOrEqual + 1;
        } else {
            return -1;
        }
    }
}
```
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
```java 
    /*
*  interface Dictionary {
*    public Integer get(int index);
*  }
*/

// You do not need to implement the Dictionary interface.
// You can use it directly, the implementation is provided when testing your solution.
public class Solution {
    public int search(Dictionary dict, int target) {
        if (dict == null) {
            return -1;
        }
        int left = 0;
        int right = 1;
        while (dict.get(right) != null && dict.get(right) < target) {
            left = right;
            right = right * 2;
        }
        return binarySearch(dict, target, left, right);
    }

    private int binarySearch(Dictionary dict, int target, int left, int right) {
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (dict.get(mid) == null || dict.get(mid) > target) {
                right = mid - 1;
            } else if (dict.get(mid) < target) {
                left = mid + 1;
            } else {
                return mid;
            }
        }
        return -1;
    }
}
```



