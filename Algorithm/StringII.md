## String II

### Reverse String
思路：swap
```java
public class Solution {
    public String reverse(String input) {
        char[] array = input.toCharArray();
        int i = 0;
        int j = array.length - 1;
        while (i < j) {
            swap(array, i++, j--);
        }
        return new String(array);
    }

    private void swap(char[] array, int a, int b) {
        char temp = array[a];
        array[a] = array[b];
        array[b] = temp;
    }
}
```

### Reverse Words In A Sentence I
```java
/**
    先把整个句子reverse一遍，再对里面的每个单词都reverse
    reverse句子则用in-place的收尾双指针交换来reverse
    对于句子内部的单词，用两个指针同时出发，让快指针寻找空格的位置，在进行inplace的reverse，
    tips：在一个单词reverse完成后，两个指针的位置都要更新到j+1的位置，即空格的后一位
*/
public class Solution {
    public String reverseWords(String input) {
        if (input == null || input.length() == 0) {
            return input;
        }
        char[] array = input.toCharArray();
        reverse(array, 0, array.length - 1);
        int slow = 0;
        for (int i = 0; i < array.length; i++) {
            if (array[i] != ' ' && (i == 0 || array[i - 1] == ' ')) {
                slow = i;
            }
            if (array[i] != ' ' && (i == array.length - 1 || array[i + 1] == ' ')) {
                reverse(array, slow, i);
            }
        }
        return new String(array);
    }

    private void reverse (char[] array, int i, int j) {
        while (i < j) {
            swap(array, i++, j--);
        }
    }

    private void swap(char[] array, int a, int b) {
        char temp = array[a];
        array[a] = array[b];
        array[b] = temp;
    }
}
```


### Right Shift By N Characters
思路：
step1: 先整体reverse  
step2: 再对两部分分别反转  
abc ef -> fe cba -> ef abc  
time: O(n) space: O(1)  
input is not null, n >= 0
```java
public class Solution {
    public String rightShift(String input, int n) {
        if (input.length() == 0 || n == 0) {
            return input;
        }
        //n如果大于input长度，先取模
        if (n > input.length()) {
            n = n % input.length();
        }
        char[] array = input.toCharArray();
        reverse(array, 0, input.length() - 1);
        reverse(array, 0, n - 1);
        reverse(array, n, input.length() - 1);
        return new String(array);
    }

    private void reverse(char[] array, int i, int j) {
        while (i < j) {
            swap(array, i++, j--);
        }
    }

    private void swap(char[] array, int a, int b) {
        char temp = array[a];
        array[a] = array[b];
        array[b] = temp;
    }
}
```

### String Replace (basic)
思路：首先要比较source和target的长度，然后看最后的string是会拉长还是缩短  
input, source and target are not null  

```java
public class Solution {
    /**
     * test case
     * @param args
     */
    public static void main(String[] args) {
        Solution test = new Solution();
        System.out.println(test.replace("student", "den", "XX"));
        System.out.println(test.replace("studentdededent", "den", "XXXX"));
        System.out.println(test.replace("studentdededent", "den", "abcd"));
        System.out.println(test.replace("", "den", "abcd"));
    }

    public String replace(String input, String source, String target) {
        if (input.length() == 0 || target.length() == 0) {
            return input;
        }
        char[] inputArray = input.toCharArray();
        char[] sourceArray = source.toCharArray();
        char[] targetArray = target.toCharArray();
        //分两种情况，做不同的处理
        if (sourceArray.length >= targetArray.length) {
            return shorterOperation(inputArray, sourceArray, targetArray);
        } else {
            return longerOperation(inputArray, sourceArray, targetArray);
        }
    }

    /**
     * source.length >= target.length
     */
    private String shorterOperation (char[] inputArray, char[] sourceArray, char[] targetArray) {
        int slow = 0;
        for (int fast = 0; fast < inputArray.length; fast++) {
            if (inputArray[fast] != sourceArray[0]) {//首元素不同，肯定不是substring，直接copy
                inputArray[slow++] = inputArray[fast];
            } else {//首元素相同，检查是否为substring
                if (checkIfSubString(inputArray, sourceArray, fast)) { //是substring
                    for (int i = 0; i < targetArray.length; i++) {//copy
                        inputArray[slow++] = targetArray[i];
                    }
                    fast = fast + sourceArray.length - 1;
                } else {//不是substring的话，要把当前元素copy进来
                    inputArray[slow++] = inputArray[fast];
                }
            }
        }
        return new String(inputArray, 0, slow);
    }

    /**
     * source.length < target.length
     */
    private String longerOperation (char[] inputArray, char[] sourceArray, char[] targetArray) {
        //先scan一遍input，记录下有多少个substring
        int counter = 0;
        int[] subStringBeginIndex = new int[inputArray.length];
        for (int i = 0; i < inputArray.length; i++) {
            if (inputArray[i] == sourceArray[0] && checkIfSubString(inputArray, sourceArray, i)) {
                subStringBeginIndex[i] = 1; //记录下对应位置为1
                i = i + sourceArray.length - 1;
                counter++;
            }
        }

        //优化：如果没有substring，直接返回
        if (counter == 0) {
            return new String(inputArray);
        }
        
        //新建一个增长的array
        char[] newInput = new char[inputArray.length + counter * (targetArray.length - sourceArray.length)];

        //然后两个指针要双向而行，从后向前copy
        int end = newInput.length - 1;
        for (int i = 0; i < inputArray.length; i++) {
            if (subStringBeginIndex[i] == 1) {//如果到了substring的开头index就要copy target
                //此处要注意：target的指针不需要从后往前走，因为newInput指针已经反着走了
                for (int j = 0; j < targetArray.length; j++) {
                    newInput[end--] = targetArray[j];
                }
                //copy完之后，不能忘记把指针移动到对应的位置，也就是移动到substring的末尾位置
                i = i + sourceArray.length - 1;
            } else {
                newInput[end--] = inputArray[i];
            }
        }
        reverse(newInput, 0, newInput.length - 1);
        return new String(newInput);
    }

    /**
     * Check if it's a substring
     */
    private boolean checkIfSubString (char[] inputArray, char[] sourceArray, int fast) {
        for (int i = 0; i < sourceArray.length; i++) {
            if (fast >= inputArray.length || inputArray[fast++] != sourceArray[i]) {
                return false;
            }
        }
        return true;
    }

    /**
     * Reverse the whole string
     */
    private void reverse(char[] array, int i, int j) {
        while (i < j) {
            char temp = array[i];
            array[i] = array[j];
            array[j] = temp;
            i++;
            j--;
        }
    }
}
```

### All Permutations II
思路：DFS，但要在循环前面加一个hashSet剪枝    
每次交换过来的元素，放入hashSet，如果后面还出现相同的元素就不要继续做   
time: O(n * n!) space: O(n^2)
```java
public class Solution {
    public List<String> permutations(String input) {
        List<String> list = new ArrayList<>();
        if (input == null) {
            return list;
        }
        char[] array = input.toCharArray();
        DFS(array, 0, list);
        return list;
    }

    private void DFS(char[] array, int index, List<String> list) {
        if (index == array.length) {
            list.add(new String(array));
            return;
        }
        Set<Character> used = new HashSet<>();
        for (int i = index; i < array.length; i++) {
            if (used.contains(array[i])) {
                continue;
            }
            used.add(array[i]);
            swap(array, i, index);
            DFS(array, index + 1, list);
            swap(array, i, index);
        }
    }

    private void swap(char[] array, int a, int b) {
        char temp = array[a];
        array[a] = array[b];
        array[b] = temp;
    }
}
```

### ReOrder Array
思路：
1. 判断array的长度为奇数还是偶数，如果是奇数，则无视最后一个元素
2. 将array从中间开始分成更小的段落，并将处于正中央的两段交换位置（这里用到string的reverse， 即先将整体reverse，再将两端分别reverse）  
3. recursion rule： 将换完的左半段和右半段做相同的操作，注意计算的都是index

1 2 3 A B C
l   m     r 
size = right - left + 1
mid = left + size / 2;
lm = left + size / 4
rm = left + (size * 3) / 4
```java
public class Solution {
    public int[] reorder(int[] array) {
        if (array.length % 2 == 0) {
            reOrder(array, 0, array.length - 1);
        } else {
            reOrder(array, 0, array.length - 2);
        }
        return array;
    }

    private void reOrder(int[] array, int left, int right) {
        int size = right - left + 1;
        if (size <= 2) {
            return;
        }
        int mid = left + size / 2;
        int lm = left + size / 4;
        int rm = left + size * 3 / 4;

        reverse(array, lm, mid - 1);
        reverse(array, mid, rm - 1);
        reverse(array, lm, rm - 1);

        //这里需要注意index的计算方法，中间分界的index就是左侧长度的两倍
        reOrder(array, left, left + 2 * (lm - left) - 1);
        reOrder(array, left + 2 * (lm - left), right);
    }

    private void reverse(int[] array, int i, int j) {
        while (i < j) {
            int temp = array[i];
            array[i] = array[j];
            array[j] = temp;
            i++;
            j--;
        }
    }
}
```

### Compress String II
思路：先scan一遍，对于重复字母直接做compress，对于单个字母先不要操作，记录下他们出现的次数
```java
public class Solution {
    public String compress(String input) {
        if (input == null || input.length() == 0) {
            return input;
        }
        char[] array = input.toCharArray();
        return compress(array);

    }

    private String compress(char[] array) {
        //先做scan，两个pointer
        int slow = 0;
        int fast = 0;
        int newLength = 0;//记录之后需要生成的array的长度
        while (fast < array.length) {
            //begin这个值其实就是fast每次到一个新元素的第一个值，不能用slow代替
            int begin = fast;
            while (fast < array.length && array[fast] == array[begin]) {
                fast++;
            }
            array[slow++] = array[begin];//把char写进去
            //下面需要判断这个char重复了多少次
            //分两种情况，一个是只有1个，另一种是重复了多次
            if (fast - begin == 1) {
                newLength += 2;//此处加2是因为单个元素需要占用两个长度去记录
            } else {//如果不止一个，就要看重复的次数，这个数字是几位数
                int len = checkDigit(array, slow, fast - begin); //得到数字长度，几位数
                slow += len; // 因为方法中的slow跟这里的slow不是一个，这里的也需要移动
                newLength += len + 1;
            }
        }
        //scan完之后，从后向前走，遇到数字copy两格，遇到字母就写 1 和 数字
        char[] newArray = new char[newLength];
        int end = newLength - 1;
        slow = slow - 1;
        while (slow >= 0) {
            if (Character.isDigit(array[slow])) {
                while (slow >= 0 && Character.isDigit(array[slow])) {
                    newArray[end--] = array[slow--];
                }
            } else {
                newArray[end--] = '1';
            }
            newArray[end--] = array[slow--];
        }
        return new String(newArray);
    }

    private int checkDigit(char[] array, int index, int count) {
        int len = 0;
        for (int i = count; i > 0; i /= 10) {
            len++;
            index++;
        }
        for (int i = count; i > 0; i /= 10) {
            int digit = i % 10;
            array[--index] = (char)('0' + digit);
        }
        return len;
    }
}
```

### Decompress String II
思路：先处理所有不会加长的string，也就是a0，a1，a2，再处理会变长的
```java
public class Solution {
    public String decompress(String input) {
        if (input.isEmpty()) {
            return input;
        }
        char[] array = input.toCharArray();
        return decodeLong(array, decodeShort(array));
    }

    //处理变短的，可以用两个指针
    private int decodeShort (char[] array) {
        int end = 0;
        for (int i = 0; i < array.length; i += 2) {
            int digit = getDigit(array[i + 1]);
            if (digit >= 0 && digit <= 2) {
                for (int j = 0; j < digit; j++) {
                    array[end++] = array[i];
                }
            } else {//如果长度大于2，就把字母和数字copy过来
                array[end++] = array[i];
                array[end++] = array[i + 1];
            }
        }
        return end;//返回目前的长度
    }

    private String decodeLong(char[] array, int length) {
        int newLength = length;
        for (int i = 0; i < length; i++) {
            int digit = getDigit(array[i]);
            if (digit > 2 && digit <= 9) {
                newLength += digit - 2;
            }
        }
        char[] result = new char[newLength];
        //从后往前copy
        int end = newLength - 1;
        for (int i = length - 1; i >= 0; i--) {
            int digit = getDigit(array[i]);
            if (digit > 2 && digit <= 9) {
                i--;
                for (int j = 0; j < digit; j++) {
                    result[end--] = array[i];
                }
            } else {
                result[end--] = array[i];
            }
        }
        return new String(result);
    }

    private int getDigit(char digitChar) {
        return digitChar - '0';
    }
}
```

### Longest Substring Without Repeating Characters
思路：sliding window，利用两个指针，当fast的值没有出现在set中，fast++；如果出现了重复元素，slow++，并且从set中remove掉
```java
public class Solution {
    public int longest(String input) {
        if (input.isEmpty()) {
            return 0;
        }
        Set<Character> distinct = new HashSet<>();
        int slow = 0;
        int fast = 0;
        int longest = 0;
        while (fast < input.length()) {
            if (!distinct.contains(input.charAt(fast))) {
                distinct.add(input.charAt(fast++));
                longest = Math.max(longest, fast - slow);
            } else {
                distinct.remove(input.charAt(slow++));
            }
        }
        return longest;
    }
}
```

### All Anagrams
Find all occurrence of anagrams of a given string s in a given string l. Return the list of starting indices.

Assumptions

sh is not null or empty.
lo is not null.
Examples

l = "abcbac", s = "ab", return [0, 3] since the substring with length 2 starting from index 0/3 are all anagrams of "ab" ("ab", "ba").

思路：sliding window，fixed长度，右边加一个，左边就要去掉一个
```java
public class Solution {
    public List<Integer> allAnagrams(String sh, String lo) {
        List<Integer> list = new ArrayList<>();
        if (lo.length() == 0) {
            return list;
        }
        if (sh.length() > lo.length()) {
            return list;
        }
        Map<Character, Integer> map = countMap(sh);
        int match = 0;

        for (int i = 0; i < lo.length(); i++) {
            //首先是看右侧，往里加元素
            char currChar = lo.charAt(i);
            Integer count = map.get(currChar);
            if (count != null) {
                map.put(currChar, count - 1);
                if (count == 1) {
                    match++;
                }
            }
            //左侧需要被移除的元素
            if (i >= sh.length()) {
                currChar = lo.charAt(i - sh.length());
                count = map.get(currChar);
                if (count != null) {
                    map.put(currChar, count + 1);
                    if (count == 0) {
                        match--;
                    }
                }
            }
            if (match == map.size()) {
                list.add(i - sh.length() + 1);//把上面被移除的元素的后一位的index加入到list
            }
        }
        return list;
    }

    private Map<Character, Integer> countMap(String s) {
        Map<Character,Integer> map = new HashMap<>();
        for (char ch : s.toCharArray()) {
            if (!map.containsKey(ch)) {
                map.put(ch, 1);
            } else {
                map.put(ch, map.get(ch) + 1);
            }
        }
        return map;
    }
}
```


### Longest subarray contains only 1s

Given an array of integers that contains only 0s and 1s and a positive integer k, you can flip at most k 0s to 1s, return the longest subarray that contains only integer 1 after flipping.

```java
public class Solution {
    public int longestConsecutiveOnes(int[] nums, int k) {
        if (nums.length == 0) {
            return 0;
        }
        int count = 0;
        int slow = 0;
        int fast = 0;
        int longest = 0;
        while (fast < nums.length) {
            if (nums[fast] == 1) {//是1的时候直接加
                fast++;
                longest = Math.max(longest, fast - slow);
            } else if (count < k) { //不是1但依旧符合条件的时候
                count++;
                fast++;
                longest = Math.max(longest, fast - slow);
            } else if (nums[slow] == 0) { //不是1，且count >= k,移动sliding window的左侧，吐出元素
                count--;
                slow++;
            }
        }
        return longest;
    }
}
```


