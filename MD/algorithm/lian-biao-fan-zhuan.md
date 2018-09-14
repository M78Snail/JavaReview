```java
/*
public class ListNode {
    int val;
    ListNode next = null;
 
    ListNode(int val) {
        this.val = val;
    }
}*/
public class Solution {
    public ListNode ReverseList(ListNode head) {
        if (head == null) {
            return null;
        }
        ListNode first = head;
        ListNode two = first.next;
        ListNode three = null;
        if (two != null) {
            three = two.next;
        }
        first.next = null;
        if (two != null) {
            while (three != null) {
                two.next = first;
                first = two;
                two = three;
                three = three.next;
            }
            two.next = first;
        } else {
            return head;
        }
        return two;
    }
}

```



