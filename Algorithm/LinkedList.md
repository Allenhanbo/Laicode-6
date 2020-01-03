## LinkedList
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

### Reverse a linked list
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

### Find middle node of linked list
online vs offline  
fast slow pointer  

Find the middle node of a given linked list.

Examples

L = null, return null  
L = 1 -> null, return 1  
L = 1 -> 2 -> null, return 1  
L = 1 -> 2 -> 3 -> null, return 2  
L = 1 -> 2 -> 3 -> 4 -> null, return 2  


Clarification: 
1. which one is middle one(even/odd length), the former one is better
2. null

```java
    /**
 * class ListNode {
 *   public int value;
 *   public ListNode next;
 *   public ListNode(int value) {
 *     this.value = value;
 *     next = null;
 *   }
 * }
 */
public class Solution {
    public ListNode middleNode(ListNode head) {
        if (head == null) {
            return head;
        }
        ListNode slow = head;
        ListNode fast = head;
        while (fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }
}
```
1 2 3 5 
    s
        f


### Check if Linked list has a cycle
```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        if (head == null) {
            return false;
        }
        ListNode slow = head;
        ListNode fast = head;
        while (fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
            if (slow == fast) {
                return true;
            }
        }
        return false;
    }
}
```


### Insert into a sorted linked list
option: 创建一个dummyHead,也可以不用dummyHead

linked list的头有可能会发生改变，这样让dummyHead的数负无穷小，最后直接返回dummyHead.next

两个case:
case1: head == null || target <= head.value   
case2: target > head.value

注意clarification：升序或降序排列

```java
public class Solution {
    public ListNode insert(ListNode head, int value) {
        ListNode newNode = new ListNode(value);
        //case 1
        if (head == null || value <= head.value) {
            newNode.next = head;
            return newNode;
        }
        //case 2
        ListNode prev = head;
        while (prev.next != null && prev.next.value < value) {
            prev = prev.next;
        }
        newNode.next = prev.next;
        prev.next = newNode;
        return head;
    }
}
```

### Merge two sorted linked lists
**谁小移谁**  
The head of linked list would be changed, so a dummy head will be better  

```java
public class Solution {
    public ListNode merge(ListNode one, ListNode two) {
        // if (one == null) {
        //     return two;
        // } else if (two == null) {
        //     return one;
        // }
        //上面判断的corner case没有必要，因为在下面的代码中会处理
        ListNode dummyHead = new ListNode(0);
        ListNode cur = dummyHead;
        while (one != null && two != null) {
            if (one.value <= two.value) {
                cur.next = one;
                one = one.next;
            } else {
                cur.next = two;
                two = two.next;
            }
            cur = cur.next;
        }
        if (one == null) {
            cur.next = two;
        } else if (two == null){
            cur.next = one;
        }
        return dummyHead.next;
    }
}
```

### Reorder Linked List
Reorder the given singly-linked list 
N1 -> N2 -> N3 -> N4 -> … -> Nn -> null to be   
N1- > Nn -> N2 -> Nn-1 -> N3 -> Nn-2 -> … -> null    

这题是hard题，主要是考察多种基本操作  
基本解题思路如下：  

1. 找到LL的中点(奇数靠前一个)
2. 反转后半部分的链表
3. Merge two LLs

1 2 3 4  
  s
    f
```java
public class Solution {
    public ListNode reorder(ListNode head) {
        //corner case
        if (head == null || head.next == null) {
            return head;
        }
        //slow -> the middle node, divide the LL into two LLs
        ListNode mid = findMiddle(head);
        ListNode one = head;
        ListNode two = mid.next;
        mid.next = null;
        return merge(one, reverse(two));

    }

    private ListNode findMiddle(ListNode head) {
        //find the middle one
        ListNode slow = head;
        ListNode fast = head;
        while (fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }
    private ListNode reverse(ListNode two) {
        //Reverse LL two;
        ListNode prev = null;
        ListNode cur = two;
        while (cur != null) {
            ListNode next = cur.next;
            cur.next = prev;
            prev = cur;
            cur = next;
        }
        //at this time, the new head should be prev, cur is null
        //two = prev;
        //return two;
        return prev;
    }

    private ListNode merge(ListNode one, ListNode two) {
        //Merge two LLs
        ListNode dummyHead = new ListNode(0);
        ListNode cur = dummyHead;
        while (one != null && two != null) {
            cur.next = one;
            one = one.next;
            cur.next.next = two;
            two = two.next;
            cur = cur.next.next;
        }
        if (one == null) {
            cur.next = two;
        } else {
            cur.next = one;
        }
        return dummyHead.next;
    }
}
```

1   2   4
  
3   6   7