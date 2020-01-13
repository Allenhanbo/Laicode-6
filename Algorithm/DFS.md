> 四道DFS基础题，非常重要，主要是用来枚举所有的组合可能

DFS基本方法：
1. recursion tree一共有多少层
2. 每层有多少个状态，分出多少个叉儿

### All Subsets I
1. How many levels in the recursion tree? 3 levels
2. How many different states should we try to put on each level? 两种状态，选或不选

吃进去多少就要吐出去多少

### All Valid Permutations Of Parentheses I
recursion tree一共六层，每层要么加'('，要么加')'

当加右括号的时候有一个限制条件：左括号的个数 >= 右括号的个数

### Combinations Of Coins
recursion tree每一层每一个叉儿会动态变化，用for loop来决定  
存储结果的int[]吃进去不用吐出来，因为可以直接覆盖上去，而之前都是append的

### All Permutations I 所有排列可能
