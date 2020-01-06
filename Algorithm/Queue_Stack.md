## Queue & Stack
逻辑上实现queue stack   
现在stack接口基本不用，用Deque接口，arrayDeque实现

### Practice: implement stack by linked list
```java
public stack {
    ListNode head;
    public void push(int val) {
        ListNode newNode = new ListNode(val);
        newNode.next = head;
        head = newNode;
    }
    public Integer pop() {
        if (head == null) {
            return null;
        }
        ListNode res = head;
        head = head.next;
        res.next = null
        return res.value;
    }
    public Integer peek() {
        return head == null ? null : head.value;
    }
}
```


### How to sort number with two stacks
1. one sentence high level -> selection sort
2. data structure
3. algorithm(high level -> details)

use two stacks:   
第二个stack充当output和buffer两个功能   
巧妙利用output元素比globalmin小的性质，来区分两部分  
如果有重复元素，记录一个counter来记录出现了几次
```java
public class Solution {
    public void sort(LinkedList<Integer> s1) {
        if (s1 == null || s1.size() <= 1) {
            return;
        }
        LinkedList<Integer> s2 = new LinkedList<Integer>();
        //s1 is input
        //s2 is both output and buffer
        sort(s1, s2);    
    }
    private void sort(Deque<Integer> input, Deque<Integer> buffer) {
        while (!input.isEmpty()) {
            int curMin = Integer.MAX_VALUE;
            int count = 0;
            while (!input.isEmpty()) {
                int cur = input.pollFirst();
                if (cur < curMin) {
                    curMin = cur;
                    count = 1;
                } else if (cur == curMin) {
                    count++;
                }
                buffer.offerFirst(cur);
            }
            while (!buffer.isEmpty() && buffer.peekFirst() >= curMin) {
                int temp = buffer.pollFirst();
                if (temp != curMin) {
                    input.offerFirst(temp);
                }
            }
            while (count > 0) {
                buffer.offerFirst(curMin);
                count--;
            }
        }
        while (!buffer.isEmpty()) {
            input.offerFirst(buffer.pollFirst());
        }
    }
}
```
**总结**: Clarification很重要，要提前确定好原始数组中
1. 是否有重复以及数据不存在Integer.MAX_VALUE or Integer.MIN_VALUE
2. 需要最终返回原始数组还是buffer数组还是都可以
3. 返回的数组中从top -> bottom要升序排列还是降序排列（关系到找globalMin or globalMax）


### Implement queue with two stacks
stack1  
stack2  
push元素都放进stack1
pop元素的时候，把stack1元素倒进stack2

time: amortized -> O(3) -> O(1)
```java
public class Solution {
    private Deque<Integer> input;
    private Deque<Integer> output;
    public Solution() {
        input = new ArrayDeque<>();
        output = new ArrayDeque<>();
    }
    public Integer poll() {
        if (output.size() == 0) {
            while (!input.isEmpty()) {
                output.offerFirst(input.pollFirst());
            }
        }
        return output.pollFirst();
    }
    public void offer(int element) {
        input.offerFirst(element);        
    }
    public Integer peek() {
        if (output.size() == 0) {
            while (!input.isEmpty()) {
                output.offerFirst(input.pollFirst());
            }
        }
        return output.peekFirst();
    }
    public int size() {
        return input.size() + output.size();
    }
    public boolean isEmpty() {
        return this.size() == 0;
    }
}
```
### Implement min() function using a stack with O(1)
维护一个min()方法，用O1的时间复杂度

Enhance the stack implementation to support min() operation. min() should return the current minimum value in the stack. If the stack is empty, min() should return -1.
- push(int element) - push the element to top
- pop() - return the top element and remove it, if the stack is empty, return -1
- top() - return the top element without remove it, if the stack is empty, return -1
- min() - return the current min value in the stack.

要注意的是，可以如何优化存储的空间，如果有大量重复元素的话  
一个比较好的解决思路是，记录下当前最小值第一次出现的位置  
```java
public class Solution {
    private Deque<Integer> stack;
    private Deque<Pair> record;
    public Solution() {
        stack = new ArrayDeque<>();
        record = new ArrayDeque<>();
    }
    public int pop() {
        if (stack.size() == 0) {
            return -1;
        }
        if (stack.size() == record.peekFirst().loc) {
            record.pollFirst();
        }
        return stack.pollFirst();
    }

    public void push(int element) {
        //BugHistory
        //此处要先push进来，才能记录下此刻该element在stack中准备的位置，即size的大小
        stack.offerFirst(element);

        if (record.size() == 0 || element < record.peekFirst().val) {
            record.offerFirst(new Pair(element, stack.size()));
        }
    }

    public int top() { // peekFirst
        return stack.size() == 0 ? -1 : stack.peekFirst();
    }

    public int min() {
        return record.size() == 0 ? -1 : record.peekFirst().val;
    }

    class Pair {
        private int val;
        private int loc;

        Pair(int val, int loc) {
            this.val = val;
            this.loc = loc;
        }
    }
}
```

### Stack by queue(s)
```java
class Solution {
    private Queue<Integer> q1;
    private Queue<Integer> q2;

    public Solution() {
        q1 = new ArrayDeque<>();
        q2 = new ArrayDeque<>();
    }

    /** Push element x onto stack. */
    public void push(int x) {
        q1.offer(x);
    }

    /** Removes the element on top of the stack and returns that element. */
    public Integer pop() {
        if (q1.size() == 0) {
            return null;
        }
        while (q1.size() > 1) {
            q2.offer(q1.poll());
        }
        int res = q1.poll();
        Queue<Integer> temp = q1;
        q1 = q2;
        q2 = temp;
        return res;
    }

    /** Get the top element. */
    public Integer top() {
        Integer res = this.pop();
        if (res != null) {
            q1.offer(res);    
        }
        return res;
    }

    /** Returns whether the stack is empty. */
    public boolean isEmpty() {
        return this.top() == null;
    }
}
```


### Deque by three stacks
这一题看起来简单，其实实现deque，用两个stacks就可以。但想要优化时间复杂度，要利用第三个buffer的stack。

**实现一个move方法，如果有一边为空，则两边平均分一下**
```java
public class Solution {
    private Deque<Integer> left;
    private Deque<Integer> right;
    private Deque<Integer> buffer;

    public Solution() {
        left = new ArrayDeque<>();
        right = new ArrayDeque<>();
        buffer = new ArrayDeque<>();
    }
  
    public void offerFirst(int element) {
        left.offerFirst(element);
    }
  
    public void offerLast(int element) {
        right.offerFirst(element);
    }
  
    public Integer pollFirst() {
        move(right, left);
        return left.isEmpty() ? null : left.pollFirst();
    }
  
    public Integer pollLast() {
        move(left, right);
        return right.isEmpty() ? null : right.pollFirst();
    }
  
    public Integer peekFirst() {
        move(right, left);
        return left.isEmpty() ? null : left.peekFirst();
    }
  
    public Integer peekLast() {
        move(left, right);
        return right.isEmpty() ? null : right.peekFirst();
    }
  
    public int size() {
        return left.size() + right.size();
    }
  
    public boolean isEmpty() {
        return left.isEmpty() && right.isEmpty();
    }

    private void move(Deque<Integer> src, Deque<Integer> dest) {
        if (!dest.isEmpty()) {
            return;
        }
        int half = src.size() / 2;
        for (int i = 0; i < half; i++) {
            buffer.offerFirst(src.pollFirst());
        }
        while (!src.isEmpty()) {
            dest.offerFirst(src.pollFirst());
        }
        while (!buffer.isEmpty()) {
            src.offerFirst(buffer.pollFirst());
        }
    }
}

```



