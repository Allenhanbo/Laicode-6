### Power Of Two
这道题有两个比较好的方法  
1. 可以看这个数的bit中是不是只有一个1，其余的都是0
2. 可以利用x & (x - 1) == 0
```java
public class Solution {
    public boolean isPowerOfTwo(int number) {
        return number > 0 && (number & (number - 1)) == 0;
    }
}
```

### Number Of Different Bits
异或操作，再把bit中所有的1相加起来
```java
public class Solution {
    public int diffBits(int a, int b) {
        int xor = a ^ b;
        int count = 0;
        while (xor != 0) {
            count += xor & 1;
            xor >>>= 1;
        }
        return count;
    }
}
```

### All Unique Characters II
```java
public class Solution {
    public boolean allUnique(String word) {
        if (word.length() == 0) {
            return true;
        }
        int[] array = new int[8];
        char[] input = word.toCharArray();
        for (char c : input) {
            int row = c / 32;
            int col = c % 32;
            int bit = (array[row] >> col) & 1;
            if (bit == 1) {
                return false;
            } 
            array[row] |= 1 << col;
        }
        return true;
    }
}
```
### Reverse Bits of 32-bit Integer
```java
public class Solution {
    public long reverseBits(long n) {
        int i = 0; 
        int j = 31;
        while (i < j) {
            n = reverse(n, i, j);
            i++;
            j--;
        }
        return n;
    }
    private long reverse(long n, int i, int j) {
        long leftBit = (n >> j) & 1L;
        long rightBit = (n >> i) & 1L;
        if (leftBit != rightBit) {
            n ^= (1L << i) | (1L << j);
        }
        return n;
    }
}
```

### Hexadecimal Representation
每4个bit跟0xf做与操作，这样可以得到index，然后把里面对应的值append到后面
```java
public class Solution {
    public static void main(String[] args) {
        Solution test = new Solution();
        System.out.println(test.hex(255));
    }
    public String hex(int number) {
        StringBuilder sb = new StringBuilder("0x");
        char[] base = new char[]{'0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'A', 'B', 'C', 'D', 'E', 'F'};
        if (number == 0) {
            return sb.append('0').toString();
        }
        boolean mark = false;
        for (int i = 28; i >= 0; i -= 4) {
            int index = (number >> i) & 0xF;
            if (index != 0) {
                mark = true;
            }
            if (mark) {
                sb.append(base[index]);
            }
        }
        return sb.toString();
    }
}
```
