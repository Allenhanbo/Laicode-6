### Recursion
把大问题变成一个更小的问题去解决  

1. base case  
2. recursion rule  

#### Fibonacci sequence: 
>time: 每一个step相加 O(2^n)  
>space: 最多压栈多少层，recursion tree左侧直上直下的个数 O(n)   

```java 
//recursion way
public class Solution {
    public int fibonacci(int K) {
        if (K < 0) {
            return 0;
        }
        //base case
        if (K == 0) {
            return 0;
        } else if (K == 1) {
            return 1;
        }
        return fibonacci(K-1) + fibonacci(K-2); //recursion rull
    }
}

```

#### power of n
**question: calculate a^b**  
solution: a^(b/2) * a^(b/2)   
base case: b == 0 -> return 1;  
recursion rule -> b is even or odd number  

best solution: calculate half result then double

>time: O(log b) 因为每次都除以2  
>space: O(log b)   

recursion的空间就是直上直下一条路径，看看压栈多少层，再看一下每层都用了多少空间。在这里每层都是O(1)， 然后一共压栈log b层。

```java
// a^b
public class Solution {
    public long power(int a, int b) {
        //base case
        if (b == 0) {
            return 1;
        }
        long halfRes = power(a, b/2);
        if (b % 2 == 0) {
            return halfRes * halfRes;
        } else {
            return halfRes * halfRes * a;
        }
    }
}

```
### Sorting Algorithm
#### Selection sort
n个元素做n轮，每轮从剩余元素中找到最小值  
Time: 1+2+3+...+n -> O(n^2)  
**Space: O(1)**  
空间复杂度是selection sort的优势，当空间比较紧张的时候可以选择这个方法。  

```java   
public class Solution {
    public int[] solve(int[] array) {
        //corner case
        if (array == null || array.length == 0) {
            return array;
        }
        //time:O(n^2), two for loops
        for (int i = 0; i < array.length; i++) {
            int globalMin = array[i];
            for (int j = i; j < array.length; j++) {
                if (array[j] < globalMin) {
                    globalMin = array[j];
                    swap(array, i , j);
                }
            }
        }
        return array;
    }
    private void swap(int[] array, int a, int b) {
        int temp = array[a];
        array[a] = array[b];
        array[b] = temp;
    }
}
```

#### 举一反二
sort with two additional stacks in one stack.  
stack1(input) [1 3 2 4  
stack2(buffer)[  
stack3(output)[  


#### Merge Sort
recursion, 谁小移谁  

上半分，下半合  
time: O(n) + O(nlogn) = O(nlogn)  
space: O(n), 还是看recursion的层数以及每一层用了多少额外空间(利用helper array可以减少额外空间使用)
[![lnUKhV.md.png](https://s2.ax1x.com/2019/12/29/lnUKhV.md.png)](https://imgchr.com/i/lnUKhV)
```java
public class Solution {
    public int[] mergeSort(int[] array) {
        if (array == null || array.length == 0) {
            return array;
        }
        int[] helper = new int[array.length];
        int left = 0;
        int right = array.length - 1;
        mergeSort(array, helper, left, right);
        return array;
    }

    private void mergeSort(int[] array, int[] helper, int left, int right) {
        if (left == right) {
            return;
        }
        int mid = left + (right - left) / 2;
        mergeSort(array, helper, left, mid);
        mergeSort(array, helper, mid + 1, right);
        merge(array, helper, left, mid, right);

    }
    private void merge(int[] array, int[] helper, int left, int mid, int right){
        for (int i = left; i <= right; i++) {
            helper[i] = array[i];
        }
        int leftIndex = left;
        int rightIndex = mid + 1;
        while (leftIndex <= mid && rightIndex <= right) {
            if (helper[leftIndex] <= helper[rightIndex]) {
                array[left++] = helper[leftIndex++];
            } else {
                array[left++] = helper[rightIndex++];
            }
        }
        while (leftIndex <= mid) {
            array[left++] = helper[leftIndex++];
        }
    }
}
```



#### Quick Sort
select pivot  
每次做完一轮，pivot就到了自己应该在的位置  
挡板法：两个挡板，三个区域，规定好三个区域的物理意义  
[0..i): 比pivot小的数  
[i..j]: 为探索区域  
(j..n-1]: 比pivot大的数  

**worse case**:  
time: O(n^2), space: O(n)  
**average**:  
time:O(nlog n), space: O(logn)


```java
public class Solution {
    public int[] quickSort(int[] array) {
        if(array == null || array.length == 0) {
            return array;
        }
        quickSort(array, 0, array.length - 1);
        return array;
    }

    private void quickSort(int[] array, int left, int right) {
        //base case
        if (left >= right) {
            return;
        }

        int pivotPos = partition(array, left, right);

        quickSort(array, left, pivotPos - 1);
        quickSort(array, pivotPos + 1, right);
    }

    private int partition(int[] array, int left, int right) {
        Random r = new Random();
        int pivotIndex = left + r.nextInt(right - left + 1);
        int pivot = array[pivotIndex];
        swap(array, pivotIndex, right);
        int leftBound = left; 
        int rightBound = right - 1;
        while (leftBound <= rightBound) {
            if (array[leftBound] < pivot) {
                leftBound++;
            } else if(array[rightBound] >= pivot) {
                rightBound--;
            } else {
                swap(array, leftBound++, rightBound--);
            }
        }
        swap(array, leftBound, right);
        return leftBound;
    }

    private void swap(int[] array, int a, int b) {
        int temp = array[a];
        array[a] = array[b];
        array[b] = temp;
    }
}
```

#### Q7 Array shuffling
moving all "0s" to right end of array  
两个挡板，三个区域    
[i..j] -> 未探索区域  
 1 2 3 0 1 2 0 0 0
 i               j 
```java
public class Solution {
    public int[] moveZero(int[] array) {
        //corner case
        if (array == null || array.length == 0) {
            return array;
        }
        int i = 0;
        int j = array.length - 1;
        while (i <= j) {
            if (array[i] == 0) {
                swap(array, i, j);
                j--; // 这里不能i++， 因为不确定j换过来的数是不是0
            } else {
                i++;
            }
        }
        return array;
    }

    private void swap (int[] array, int a, int b) {
        int temp = array[a];
        array[a] = array[b];
        array[b] = temp;
    }
}
```


#### Q8 Rainbow Sort
三个挡板，四个区域
[..i):  
[i..j): 
[j..k]: 
(k..n]: 
```java
public class Solution {
    public int[] rainbowSort(int[] array) {
        if (array == null || array.length == 0) {
            return array;
        }
        int i = 0;
        int j = 0;
        int k = array.length - 1;

        while (j <= k) {
            if (array[j] == -1) {
                swap(array, i ,j);
                i++;
                j++;
            } else if (array[j] == 0) {
                j++;
            } else {
                swap(array, j, k);
                k--;
            }
        }
        return array;
    }
    private void swap (int[] array, int a, int b) {
        int temp = array[a];
        array[a] = array[b];
        array[b] = temp;
    }
}
```