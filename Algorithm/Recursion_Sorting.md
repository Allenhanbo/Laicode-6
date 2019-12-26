### Recursion
把大问题变成一个更小的问题去解决  

1. base case  
2. recursion rule  

#### Fibonacci sequence: 
time: 每一个step相加 O(2^n)  
space: 最多压栈多少层，recursion tree左侧直上直下的个数 O(n)  

#### power of n
**question: calculate a^b**  
solution: a^(b/2) * a^(b/2)   
base case: b == 0 -> return 1;  
recursion rule -> b is even or odd number  


### Sorting Algorithm
#### Selection sort
n个元素做n轮，每轮从剩余元素中找到最小值  
Time: 1+2+3+...+n -> O(n^2)  
Space: O(1)  
空间复杂度是selection sort的优势，当空间比较紧张的时候可以选择这个方法。  

#### 举一反二
sort with two additional stacks in one stack.  
stack1(input) [1 3 2 4  
stack2(buffer)[  
stack3(output)[  

#### Merge Sort
recursion, 谁小移谁  


#### Quick Sort
select pivot  
每次做完一轮，pivot就到了自己应该在的位置  
挡板法：两个挡板，三个区域，规定好三个区域的物理意义  
[0..i): 比pivot小的数  
[i..j]: 为探索区域  
(j..n-1]: 比pivot大的数  

worse case:  
time: O(n^2), space: O(n)

average:  
time:O(nlog n), space: O(logn)

#### Q7 Array shuffling
moving all "0s" to right end of array  
两个挡板，三个区域  

#### Q8 Rainbow Sort
三个挡板，四个区域
[0..i):  
[i..j): 
[j..k]: 
(k..n]: 