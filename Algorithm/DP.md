## DP I

### Fibonacci Number
```java
public class Solution {
    public long fibonacci(int K) {
        long a = 0;
        long b = 1;
        if (K <= 0) {
            return a;
        }
        while (K > 1) {
            long temp = a + b; 
            a = b;
            b = temp;
            K--;
        }
        return b;
    }
}
```

### Longest Ascending SubArray
思路：记录到当前元素，升序是多少个，然后每次更新连续升序的个数的时候，更新globalMax
```java
public class Solution {
    public int longest(int[] array) {
        if (array.length == 0) {
            return 0;
        }
        int currMax = 1;
        int globalMax = 1;
        for (int i = 1; i < array.length; i++) {
            if (array[i] > array[i - 1]) {
                currMax++;
                globalMax = Math.max(globalMax, currMax);
            } else {
                currMax = 1;
            }
        }
        return globalMax;
    }
}
```

### Max Product Of Cutting Rope
思路：左大段，右小段思想。
内层for循环算出当前绳子长度i的情况下，最大的product值
```java
public class Solution {
    public int maxProduct(int length) {
        //assume length >= 2
        int[] M = new int[length + 1];
        M[1] = 0; //初始值
        for (int i = 2; i <= length; i++) { //绳子的长度 length from 2
            for (int j = 1; j < i; j++) { //计算当前长度下的最大值, j是左侧切的大段，j右侧是小段
                M[i] = Math.max(M[i], Math.max(j, M[j]) * (i - j));
            }
        }
        return M[length];
    }
}
```

### Array Hopper I
```java
/**
  tips: 此题从后往前linear scan，并且最后一个格子被设为true， 所以直接从倒数第二个格子开始向前scan
  只要在该格子允许的步数内array[i], 向后看是否有为true的格子，有则该格子为true，否则为false； 一旦遇到true，则要break
  最后return M[0]
*/
public class Solution {
    public boolean canJump(int[] array) {
        boolean[] jumpOrNot = new boolean[array.length];
        jumpOrNot[array.length - 1] = true;
        for (int i = array.length - 2; i >= 0; i--) {
            for (int j = i + 1; j <= i + array[i]; j++) {
                if (jumpOrNot[j] == true) {
                    jumpOrNot[i] = true;
                    break;
                }
            }
        }
        return jumpOrNot[0];
    }
}

```


## DP II

### Array Hopper II
DP的思路，从后向前做，小于零则为false，如果大于零，则表示为true并且跳到为true的格子最小的步数
```java
public class Solution {
    public int minJump(int[] array) {
        int[] M = new int[array.length];
        M[array.length - 1] = 0;
        for (int i = array.length - 2; i >= 0; i--) {
            if (array[i] <= 0) {
                M[i] = -1;
            } else {
                int min = Integer.MAX_VALUE;
                for (int j = i + 1; j <= i + array[i]; j++) {
                    if (j < array.length && M[j] >= 0) {
                        min = Math.min(min, M[j]); 
                    }
                }
                M[i] = min + 1;
            }
        }
        return M[0] < 0 ? -1 : M[0];
    }   
}
```


### Largest SubArray Sum
这一题个人感觉很有意思，也很基础。用DP的思维可以非常快的得到结果。这一题的物理意义需要声明清楚，M[i]代表以i为end，包括i这个值在内的subarray的最大的sum是多少
```java
public class Solution {
    public int largestSum(int[] array) {
        int[] M = new int[array.length];
        M[0] = array[0];
        int globalMax = M[0];
        for (int i = 1; i < array.length; i++) {
            M[i] = M[i - 1] >= 0 ? M[i - 1] + array[i] : array[i];
            globalMax = Math.max(M[i], globalMax);
        }
        return globalMax;
    }
}
```
空间复杂度可以进一步优化到O(1)，因为我们只需要回头看M[i - 1]的值，所以把这个值记录下来即可
```java
public class Solution {
    public int largestSum(int[] array) {
        int prev = array[0];
        int globalMax = prev;
        for (int i = 1; i < array.length; i++) {
            prev = Math.max(array[i], prev + array[i]);
            globalMax = Math.max(prev, globalMax);
        }
        return globalMax;
    }
}
```