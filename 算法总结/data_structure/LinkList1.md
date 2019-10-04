### 翻转链表
呜呜呜，链表好难....嘤嘤  
有没快捷途径秒懂？(⊙o⊙)…  做梦吧
### 栈的方式
```java
class Solution{
    public static ListNode ReverseList(ListNode head) {
        if (head == null || head.next == null)
            return head;
        // 存储的栈
        LinkedList<ListNode> stack = new LinkedList<>();
        // 定义一个结果指针和结果移动指针
        ListNode ans = new ListNode(-1);
        ListNode ansMov = ans;
        // 用来方便遍历的指针
        ListNode cur = head;
        // 循环遍历入栈，加入新节点，防止指针乱指
        while(cur != null){
            ListNode tmp = new ListNode(cur.val);
            stack.push(tmp);
            cur = cur.next;
        }
        // 循环遍历出栈中元素
        while(!stack.isEmpty()){
            ansMov.next = stack.pop();
            ansMov = ansMov.next;
        }

        return ans.next;
    }
}
```
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