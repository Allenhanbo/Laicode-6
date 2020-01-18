> 四道DFS基础题，非常重要，主要是用来枚举所有的组合可能

DFS基本方法：
1. recursion tree一共有多少层
2. 每层有多少个状态，分出多少个叉儿

### All Subsets I
1. How many levels in the recursion tree? 3 levels
2. How many different states should we try to put on each level? 两种状态，选或不选

```java
public class Solution {
    public List<String> subSets(String set) {
        List<String> list = new ArrayList<>();
        if (set == null) {
            return list;
        }
        char[] input = set.toCharArray();
        StringBuilder sb = new StringBuilder();
        helper(input, sb, 0, list);
        return list;
    }
    private void helper(char[] input, StringBuilder sb, int index, List<String> list) {
        //bsae case：已经到了最后一层
        if (index == input.length) {
            list.add(sb.toString());
            return;
        }
        //case 1: 加当前index的元素
        helper(input, sb.append(input[index]), index + 1, list);
        //吃进去的要吐出来，因为用的是同一个stringbuilder
        sb.deleteCharAt(sb.length() - 1);

        //case 2: 不加当前index元素
        helper(input, sb, index + 1, list);
    }
}
```

吃进去多少就要吐出去多少

### All Valid Permutations Of Parentheses I
recursion tree一共六层，每层要么加'('，要么加')'

当加右括号的时候有一个限制条件：左括号的个数 >= 右括号的个数
```java
public class Solution {
    public List<String> validParentheses(int n) {
        List<String> list = new ArrayList<>();
        StringBuilder sb = new StringBuilder();
        int left = 0;
        int right = 0;
        helper(sb, n, left, right, list);
        return list;
    }
    private void helper(StringBuilder sb, int n, int l, int r, List<String> list) {
        //base case: 打印完所有括号的时候
        if (l + r == 2 * n) {
            list.add(sb.toString());
            return;
        }
        //加左括号
        if (l < n) {
            helper(sb.append('('), n, l + 1, r, list);
            sb.deleteCharAt(sb.length() - 1);
        }
        //加右括号
        if (r < l) {
            helper(sb.append(')'), n, l, r + 1, list);
            sb.deleteCharAt(sb.length() - 1);
        }
    }
}
```


### Combinations Of Coins
recursion tree每一层每一个叉儿会动态变化，用**for loop**来决定  
存储结果的int[]吃进去不用吐出来，因为可以直接覆盖上去，而之前都是append的
```java
public class Solution {
    public List<List<Integer>> combinations(int target, int[] coins) {
        List<List<Integer>> list = new ArrayList<>();
        List<Integer> curr = new ArrayList<>();
        helper(coins, target, 0, curr, list);
        return list; // 不要忘记return
    }

    private void helper(int[] coins, int moneyLeft, int index, List<Integer> curr, List<List<Integer>> list) {
        if (index == coins.length - 1) {
            if (moneyLeft % coins[index] == 0) {
                curr.add(moneyLeft / coins[index]);
                //BugHistory：此处要返回一个new出来的新对象
                list.add(new ArrayList<Integer>(curr));
                curr.remove(curr.size() - 1);
            }
            return;
        }
        int max = moneyLeft / coins[index];
        for (int i = 0; i <= max; i++) {
            curr.add(i);
            helper(coins, moneyLeft - coins[index] * i, index + 1, curr, list);
            curr.remove(curr.size() - 1);
        }
    }
}
```

### All Permutations I 所有排列可能
No duplicate characters
```java
public class Solution {
    public List<String> permutations(String input) {
        List<String> list = new ArrayList<>();
        if (input == null) {
            return list;
        }
        char[] inputChar = input.toCharArray();
        helper(inputChar, 0, list);
        return list;//不要忘记return
    }

    private void helper(char[] array, int index, List<String> list) {
        //corcer case + base case
        if (array.length == 0 || index == array.length - 1) { //index == array.length也可以
            list.add(new String(array));
            return;
        }
        for (int i = index; i < array.length; i++) {
            swap(array, index, i);
            helper(array, index + 1, list);
            swap(array, index, i);
        }
    }

    private void swap(char[] array, int a, int b) {
        char temp = array[a];
        array[a] = array[b];
        array[b] = temp;
    }
}

```