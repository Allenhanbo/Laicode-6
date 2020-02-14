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


### Dictionary Word I
这一题有些难度，主要是运用DP的左大段，右小段的思想。左大段从历史的数据中可以直接读出来

值得注意的一个地方是，这个M数组的长度比input的长度多了1，但是它的初始值仍然需要用，并且初始值要设置为true
```java
public class Solution {
    public boolean canBreak(String input, String[] dict) {
        Set<String> dictionary = toSet(dict);
        boolean[] M = new boolean[input.length() + 1];
        M[0] = true;
        for (int i = 1; i < M.length; i++) {
            for (int j = i - 1; j >= 0; j--) { //等于0的一次iteration就是一刀都不切的情况
                //左大段，右小段 也可以写成 for (int j = 0; j < i; j++) 相当于把切的顺序换成了从左到右
                if (M[j] && dictionary.contains(input.substring(j, i))) {
                    M[i] = true;
                    break;
                }
            }
        }
        return M[input.length()];
    }
    private Set<String> toSet(String[] dict) {
        Set<String> set = new HashSet<>();
        for (int i = 0; i < dict.length; i++) {
            set.add(dict[i]);
        }
        return set;
    }
}
```

### Edit Distance
思路：这一题其实是二维DP题，但是解题思路并不是很直观。
```java
public class Solution {
    public int editDistance(String one, String two) {
        int row = one.length();
        int col = two.length();
        int[][] M = new int[row + 1][col + 1];
        for (int i = 0; i <= row; i++) {
            M[i][0] = i;
        }
        for (int i = 0; i <= col; i++) {
            M[0][i] = i;
        }
        for (int i = 1; i <= row; i++) {
            for (int j = 1; j <= col; j++) {
                if (one.charAt(i - 1) == two.charAt(j - 1)) {
                    M[i][j] = M[i - 1][j - 1];
                    continue;
                }
                M[i][j] = Math.min(M[i - 1][j - 1] + 1, M[i][j - 1] + 1);
                M[i][j] = Math.min(M[i - 1][j] + 1, M[i][j]);
            }
        }
        return M[row][col];
    }
}
```


### Largest Square Of 1s
思路：心中画一个2D的matrix，这一题的DP解法，初始值是最上面一行和最左侧一列，他们在M中的值，就是自身的值因为他们无法构成比自身还要大的正方形了

对于M中的每一个值的物理意义是，以M[i][j]为右下角，能画出来的最大的正方形的边长
```java
public class Solution {
    public int largest(int[][] matrix) {
        int row = matrix.length;
        int col = matrix[0].length;
        int[][] M = new int[row][col];
        int globalMax = 0;

        for (int i = 0; i < row; i++) {
            for (int j = 0; j < col; j++) {
                if (i == 0 || j == 0) {
                    M[i][j] = matrix[i][j];
                } else if (matrix[i][j] == 1) {
                    M[i][j] = Math.min(M[i - 1][j - 1] + 1, M[i - 1][j] + 1);
                    M[i][j] = Math.min(M[i][j - 1] + 1, M[i][j]);
                }
                globalMax = Math.max(M[i][j], globalMax);
            }
        }
        return globalMax;
    }
}
```


## DP III
> DP III从头到位都是循序渐进的，主要是如何把二维的DP利用一维的思路去解决。
### Quiz: Largest SubArray Sum
这一题是DP II中一题的follow up，在返回最大subArray的和的时候，同时返回该最大subArray的左右边界index。

思路主要就是思考两个问题
1. 什么时候更新curr左指针
2. 什么时候更新最大subarray的和以及其左右指针

1. 在“另起炉灶”的时候更新左指针，此时的左指针就是当前值的index。如果“继承遗产”则不需要更新左指针。
2. 在每次做完决定，是“另起炉灶”还是“继承遗产”之后，判断一下当前的sum和存储的globalMax的sum，如果当前sum较大，则需要更新globalMax的sum，同时更新其左右指针。
```java
public class Solution {
    public int[] largestSum(int[] array) {
        int globalLeft = 0; 
        int globalRight = 0; 
        int globalMax = array[0];
        int currLeft = 0;
        int sum = array[0]; 
        for (int i = 1; i < array.length; i++) {
        if (sum >= 0) {
            sum += array[i];
        } else {
            sum = array[i];
            currLeft = i;
        }
        if (sum > globalMax) {
            globalRight = i;
            globalLeft = currLeft;
            globalMax = sum;
        }
        
        }
        return new int[]{globalMax, globalLeft, globalRight};
    }
}
```

### Longest Consecutive 1s
思路：主要看当前array中的值，如果是1就看看前面最长的值+1， 如果是0就东山再起。
每次如果是1的话都看看longest是否需要更新。
```java
public class Solution {
    public int longest(int[] array) {
        int longest = 0;
        int curr = 0;
        for (int i = 0; i < array.length; i++) {
            if (array[i] == 1) {
                curr += 1;
                longest = Math.max(longest, curr);
            } else {
                curr = 0;
            }
        }
        return longest;
    }
    
}
```

### Longest Cross Of 1s
思路：从四个方向，分别先做一遍DP，意义是对于任意一个matrix中的值，都能够O(1)的时间读出它的四个手臂最长的长度。然后把四个长度的手臂取最小值。最后返回这些最小值中的最大值。

这一题的答案解法写得非常好看，因为它把从左上角和从右下角开始便利的时候，每次都填写了两个DP的表格。从左上角开始的时候，相当于从上或者从左测扫，右下角相对应是从右或者从下扫。在每次扫完两个方向后，利用一个merge函数，把DP的matrix两两比较，取对应位置的较小值。同时记录一个最小值中的最大值，这样最后一次比较的时候，可以直接返回这个最大值。
```java
public class Solution {
    public int largest(int[][] matrix) {
        int row = matrix.length;
        int col = matrix[0].length;
        if (row == 0 || col == 0) {
            return 0;
        }
        int[][] upLeft = upLeft(matrix, row, col);
        int[][] downRight = downRight(matrix, row, col);

        return merge(upLeft, downRight);
    }

    private int merge(int[][] one, int[][] two) {
        int result = 0;
        for (int i = 0; i < one.length; i++) {
            for (int j = 0; j < one[0].length; j++) {
                one[i][j] = Math.min(one[i][j], two[i][j]);
                result = Math.max(result, one[i][j]);
            }
        }
        return result;
    }


    private int[][] upLeft(int[][] matrix, int row, int col) {
        int[][] up = new int[row][col];
        int[][] left = new int[row][col];
        for (int i = 0; i < row; i++) {
            for (int j = 0; j < col; j++) {
                if (matrix[i][j] == 1) {
                    if (i == 0 && j == 0) {
                        up[i][j] = 1;
                        left[i][j] = 1;
                    } else if (i == 0) {
                        up[i][j] = 1;
                        left[i][j] = left[i][j - 1] + 1;
                    } else if (j == 0) {
                        up[i][j] = up[i - 1][j] + 1;
                        left[i][j] = 1;
                    } else {
                        up[i][j] = up[i - 1][j] + 1;
                        left[i][j] = left[i][j - 1] + 1;
                    }
                }
            }
        }
        merge(up, left);
        return up;
    }

    private int[][] downRight(int[][] matrix, int row, int col) {
        int[][] down = new int[row][col];
        int[][] right = new int[row][col];
        
        for (int i = row - 1; i >= 0; i--) {
            for (int j = col - 1; j >= 0; j--) {
                if (matrix[i][j] == 1) {
                    if (i == row - 1 && j == col - 1) {
                        down[i][j] = 1;
                        right[i][j] = 1;
                    } else if (i == row - 1) {
                        down[i][j] = 1;
                        right[i][j] = right[i][j + 1] + 1;//不能忘记，如果是回头看的时候，当前格子是之前的值还要 + 1
                    } else if (j == col - 1) {
                        down[i][j] = down[i + 1][j] + 1;
                        right[i][j] = 1;
                    } else {
                        down[i][j] = down[i + 1][j] + 1;
                        right[i][j] = right[i][j + 1] + 1;
                    }
                }
            }
        }
        merge(down, right);
        return down;
    }
}
```

### Largest Square Surrounded By One
思路：先利用DP，计算左手臂和上手臂的长度，以每次的点为matrix的右下角，然后看这个matrix的左边和上边是否也都是1，可以直接读表得出，只要对应角落的点上的值，大于或等于最大的可能的边长即可
```java
public class Solution {
    public int largestSquareSurroundedByOne(int[][] matrix) {
        int row = matrix.length;
        int col = matrix[0].length;
        if (row == 0 || col == 0) {
            return 0;
        }
        int[][] left = new int[row + 1][col + 1];
        int[][] up = new int[row + 1][col + 1];
        int result = 0;
        for (int i = 0; i < row; i++) {
            for (int j = 0; j < col; j++) {
                if (matrix[i][j] == 1) {
                    left[i + 1][j + 1] = left[i + 1][j] + 1;
                    up[i + 1][j + 1] = up[i][j + 1] + 1;

                    for (int maxLength = Math.min(left[i + 1][j + 1], up[i + 1][j + 1]); maxLength >= 1; maxLength--) {
                        if (left[i + 2 - maxLength][j + 1] >= maxLength && up[i + 1][j + 2 - maxLength] >= maxLength) {
                            result = Math.max(result, maxLength);
                            break;
                        }
                    }
                }
            }
        }
        return result;
    }
}
```

### Largest X Of 1s
思路：跟正十字型的题目一样，只要换一下DP的方向从四个角落的方向去做DP
```java
public class Solution {
    public int largest(int[][] matrix) {
        int row = matrix.length;
        int col = matrix[0].length;
        if (row == 0 || col == 0) {
            return 0;
        }

        int[][] upRight = upRight(matrix, row, col);
        int[][] downLeft = downLeft(matrix, row, col);

        return merge(upRight, downLeft, row, col);
    }

    private int merge(int[][] one, int[][] two, int row, int col) {
        int result = 0;
        for (int i = 0; i < row; i++) {
            for (int j = 0; j < col; j++) {
                one[i][j] = Math.min(one[i][j], two[i][j]);
                result = Math.max(result, one[i][j]);
            }
        }
        return result;
    }

    private int[][] upRight(int[][] matrix, int row, int col) {
        int[][] up = new int[row][col];
        int[][] right = new int[row][col];
        for (int i = 0; i < row; i++) {
            for (int j = 0; j < col; j++) {
                if (matrix[i][j] == 1) {
                    up[i][j] = getNumber(up, i - 1, j + 1, row, col) + 1;
                    right[i][j] = getNumber(right, i - 1, j - 1, row, col) + 1;
                }
            }
        }
        merge(up, right, row, col);
        return up;
    }

    private int[][] downLeft(int[][] matrix, int row, int col) {
        int[][] down = new int[row][col];
        int[][] left = new int[row][col];
        for (int i = row - 1; i >= 0; i--) {
            for (int j = col - 1; j >= 0; j--) {
                if (matrix[i][j] == 1) {
                    down[i][j] = getNumber(down, i + 1, j + 1, row, col) + 1;
                    left[i][j] = getNumber(left, i + 1, j - 1, row, col) + 1;
                }
            }
        }
        merge(down, left, row, col);
        return down;
    }

    private int getNumber(int[][] matrix, int i, int j, int row, int col) {
        if (i < 0 || i >= row || j < 0 || j >= col) {
            return 0;
        }
        return matrix[i][j];
    }
}

```


### Largest SubMatrix Sum
思路：这一题答案的写法是time：n^3的写法，但是一般情况下很难想到。  

所以我先用n^4的写法来写，主要是利用DP来优化读取matrix的sum，利用DP先把matrix中，以任意值为右下角，以0，0为左上角的matrix的最大sum值算出来
```java
public class Solution {
    public static void main(String[] args) {
        Solution test = new Solution();
        int[][] matrix = new int[][]{
                {-3,2,3},
                {4,5,6},
                {7,8,-90}
        };
        int row = matrix.length;
        int col = matrix[0].length;
        int[][] M = test.DP(matrix, row,col);
        for (int i = 0; i < row; i++) {
            for (int j = 0; j < col; j++){
                System.out.print(M[i][j] + " ");
            }
            System.out.println("");
        }
        System.out.println(test.largest(matrix));
    }

    public int largest(int[][] matrix) {
        int row = matrix.length;
        int col = matrix[0].length;
        int[][] M = DP(matrix, row, col);
        int result = Integer.MIN_VALUE;
        for (int i = 0; i < row; i++) {
            for (int j = 0; j < col; j++) {
                for (int length = 1; length <= j + 1; length++) {
                    for (int height = 1; height <= i + 1; height++) {
                        //int sum = M[i][j] - M[i - height][j] - M[i][j - length + 1] + M[i - height + 1][j - length + 1];
                        //利用getNumber函数，去处理当长度和高度超过边界的情况
                        int sum = M[i][j] - getNumber(M, i - height, j) - getNumber(M, i, j - length) + getNumber(M, i - height, j - length);
                        result = Math.max(result, sum);
                    }
                }
            }
        }
        return result;
    }
    
    private int getNumber(int[][] M, int row, int col) {
        if (row < 0 || row >= M.length || col < 0 ||col >= M[0].length) {
            return 0;
        }
        return M[row][col];
    }

    private int[][] DP (int[][] matrix, int row, int col) {
        int[][] M = new int[row][col];
        for (int i = 0; i < row; i++) {
            int currSum = 0;
            for (int j = 0; j < col; j++) {
                currSum += matrix[i][j];
                if (i == 0) {
                    M[i][j] = currSum;
                } else {
                    M[i][j] = currSum + M[i - 1][j];
                }
            }
        }
        return M;
    }
}
```
