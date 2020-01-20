## HashTable

### Top K Frequent Words
hashmap可以记录下每个key出现的次数  
然后利用一个minHeap可以得到k个出现最多的值  

time: 
1. iterate string[] and put in map-> average O(n) worse O(n^2)
2. minHeap:
   1. put k into minHeap -> O(k)
   2. put n - k int minHeap -> O((n-k)logk)
space: O(n + k)

```java
public class Solution {
    public String[] topKFrequent(String[] combo, int k) {
        if (combo.length == 0) {
            return new String[0];
        }
        Map<String, Integer> freqMap = getFreqMap(combo);

        PriorityQueue<Map.Entry<String, Integer>> minHeap = new PriorityQueue<>(k, new Comparator<Map.Entry<String, Integer>>(){
            @Override
            public int compare(Map.Entry<String, Integer> e1, Map.Entry<String, Integer> e2) {
                return e1.getValue().compareTo(e2.getValue());
            }
        }); 

        for (Map.Entry<String, Integer> entry : freqMap.entrySet()) {
            if (minHeap.size() < k) {
                minHeap.offer(entry);
            } else if (entry.getValue() > minHeap.peek().getValue()) {
                minHeap.poll();
                minHeap.offer(entry);
            }
        }

        String[] res = new String[minHeap.size()];
        for (int i = minHeap.size() - 1; i >= 0; i--) {
            res[i] = minHeap.poll().getKey();
        }
        return res;
    }

    private Map<String, Integer> getFreqMap(String[] combo) {
        Map<String, Integer> map = new HashMap<>();
        for (String s : combo) {
            Integer freq = map.get(s);
            if (freq == null) {
                map.put(s, 1);
            } else {
                map.put(s, freq + 1);
            }
        }
        return map;
    }
}
```

### Missing Number I
这一题解法有很多
1. 可以用bit operation做 time:O(n) space:O(1)
2. 也可以用hashtable来做 time: average O(n), worse case O(n^2) space: O(n)
3. 利用数学的等差数列求和，算差值 time: O(n) space:O(1)
```java
//sum 
public class Solution {
    public int missing(int[] array) {
        int n = array.length + 1;
        long targetSum = (n + 0L) * (1 + n) / 2;
        long sum = 0L;
        for (int i = 0; i < array.length; i++) {
            sum += array[i];
        }
        return (int)(targetSum - sum);
    }
}

//xor way
public class Solution {
    public int missing(int[] array) {
        int n = array.length + 1;
        int xor = 0;
        for (int i = 1; i <= n; i++) {
            xor ^= i;
        }
        for (int i = 0; i < array.length; i++) {
            xor ^= array[i];
        }
        return xor;
    }
}
```

### Common Numbers Of Two Sorted Arrays(Array version)
思路：
1. 前提：sorted array， 可以用two pointers同向而行 time: O(M+N) space: O(1)
2. hashSet可以去除重复-> average O(M + N), worst: O(M^2 + N^2) space: O(M)
3. M <<<<< N,可以对每一个M中的值，在N中做binary search, time O(M*logN) space: O(1)
```java
//two pointers
public class Solution {
    public List<Integer> common(int[] A, int[] B) {
        List<Integer> res = new ArrayList<>();
        int one = 0;
        int two = 0;
        while (one < A.length && two < B.length) {
            if (A[one] == B[two]) {
                res.add(A[one]);
                one++;
                two++;
            } else if (A[one] < B[two]) {
                one++;
            } else {
                two++;
            }
        }
        return res;
    }
}
```

## String I

### Remove Certain Characters
思路：双指针，三区域的方法  
...,i]: 确定下来的部分

(i,j)：不需要care的部分，也就是被丢弃的部分

[j,...还未探索的部分

然后可以用swap的方法，in-place去做这个操作
```java
public class Solution {
    public String remove(String input, String t) {
        char[] array = input.toCharArray();
        Set<Character> set = toSet(t);

        int i = 0;
        int j = 0;
        while (j < array.length) {
            if (set.contains(array[j])) {
                j++;
            } else {
                array[i++] = array[j++]
            }
        }
        return new String(array, 0, i);
    }

    private Set<Character> toSet (String t) {
        Set<Character> set = new HashSet<>();
        for (int i = 0; i < t.length(); i++) {
            set.add(t.charAt(i));
        }
        return set;
    }
}
```

### Remove Spaces
思路：还是双指针的思路，不过有几种case需要讨论一下  
case1: 如果slow在最前面index=0的地方，那么遇到空格都要跳过  
case2: 如果slow不在最前面，2.1遇到空格的话要看看slow前面一个值是不是空；2.2不是空格就把当前的元素复制到slow的位置 
最后不能忘记要把最后的空格去掉（有可能会出现最后一个是空格的情况）
```java
public class Solution {
    public String removeSpaces(String input) {
        if (input == "") {
            return input;
        }
        char[] array = input.toCharArray();
        int end = 0;
        for (int i = 0; i < array.length; i++) {
            if (array[i] == ' ' && (end == 0 || array[i - 1] == ' ')) {
                continue;
            } else {
                array[end++] = array[i];
            }
        }
        if (end > 0 && array[end - 1] == ' ') {
            end--;
        }
        return new String(array, 0, end);
    }
}
```


### Remove Adjacent Repeated Characters I
思路：双指针, 跟slow前一个元素比较，如果相同就不copy
```java
public class Solution {
    public String deDup(String input) {
        if (input == "") {
            return input;
        }
        char[] array = input.toCharArray();
        int slow = 1;
        for (int i = 1; i < array.length; i++) {
            if (array[i] != array[slow - 1]) {
                array[slow++] = array[i];
            }
        }
        return new String(array, 0, slow);
    }
}
```

### Remove Adjacent Repeated Characters IV
Repeatedly remove all adjacent, repeated characters in a given string from left to right.

No adjacent characters should be identified in the final string.

Examples

"abbbaaccz" → "aaaccz" → "ccz" → "z"  
"aabccdc" → "bccdc" → "bdc"  

思路：利用一个stack 回头看，还是要用到two pointers
```java
public class Solution {
    public String deDup(String input) {
        if (input == null || input.length() <= 1) {
            return input;
        }
        char[] array = input.toCharArray();
        int end = 0;
        
        for (int i = 1; i < array.length; i++) {
            //元素不一样，或者stack中没有元素
            if (end == -1 || array[i] != array[end]) { // 要先判断end，防止出现exception
                end++;
                array[end] = array[i];
            } else {//相同的情况，要stack pop一个，也就是end--，然后i移到相同的最后一个元素；
                end--;
                while (i + 1 < array.length && array[i + 1] == array[i]) {
                    i++;
                }
            }
        }
        return new String(array, 0, end + 1);
    }
}
```

### Determine If One String Is Another Substring
Rabin-Karp方法有一些复杂，先用简单的方法
下面的方法时间复杂度比较高 O(n^2)
```java
public class Solution {
    public int strstr(String large, String small) {
        if (large.length() < small.length()) {
            return -1;
        }
        if (small.length() == 0) {
            return 0;
        }
        for (int i = 0; i <= large.length() - small.length(); i++) {
            int j = 0;
            while (j < small.length() && large.charAt(i + j) == small.charAt(j)) {
                j++;
            }
            if (j == small.length()) {
                return i;
            }
        }
        return -1;
    }
}
```

