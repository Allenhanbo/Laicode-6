### LinkedList
1. 防止NPE  
2. 不要丢掉head节点  
```java
    class ListNode {
        public int value;
        public ListNode next;
        // public ListNode prev;
        public ListNode(int value) {
            this.value = value;
            this.next = null;
        }
    }
```

#### Q1 Reverse a linked list
**Iteration way**
three pointers  
prev, cur, next（物理意义）
- initialize: prev = null, cur = head, next = cur.next  
- for each step:  
    1. cur.next = prev;
    2. prev = cur;
    3. cur = next;
    4. next = cur.next;
- termination condition
    cur == null

```java
public class Solution {
    public ListNode reverse(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode prev = null;
        ListNode cur = head;
        while (cur != null) {
            ListNode next = cur.next;
            cur.next = prev;
            prev = cur;
            cur = next;
        }
        return prev;
    }
}
```

**Recursive way**  
把问题变小，每一步需要做什么，想清楚subproblem
```java
public class Solution {
    public ListNode reverse(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode newHead = reverse(head.next);
        head.next.next = head;
        head.next = null;
        return newHead;
    }
}
```




#### 举一反二：Q1 Find middle node of linked list
online vs offline  
fast slow pointer  


#### Q4 Insert into a sorted linked list
创建一个dummyHead   
因为linked list的头有可能会发生改变，这样让dummyHead的数负无穷小，最后直接返回dummyHead.next


