**Question:**  
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