### 翻转链表
呜呜呜，链表好难....嘤嘤  
有没快捷途径秒懂？(⊙o⊙)…  做梦吧
### 递归方式
```java
class Solution{
    public ListNode ReverseList(ListNode head){
        if (head == null || head.next == null)
            return head;
        
        ListNode ans = ReverseList(head.next);
        head.next.next = head;
        head.next = null;
        
        return ans;
    }
}
```
### 非递归方式
```java
class Solution{
    public ListNode ReverseList(ListNode head){
        if (head == null || head.next == null)
            return head;
        
        ListNode pre  = null;
        ListNode cur  = head;
        ListNode next = head;
        while (cur != null){
            next = next.next;
            cur.next = pre;
            pre = cur;
            cur = next;
        }
        
        return pre;
    }
}
```