# 链表

## 基本技能

链表相关的核心点

- null/nil 异常处理
- dummy node 哑巴节点
- 快慢指针
- 插入一个节点到排序链表
- 从一个链表中移除一个节点
- 翻转链表
- 合并两个链表
- 找到链表的中间节点

## 常见题型

### [remove-duplicates-from-sorted-list](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

> 给定一个排序链表，删除所有重复的元素，使得每个元素只出现一次。

```go
func deleteDuplicates(head *ListNode) *ListNode {
    current := head
    for current != nil {
        // 全部删除完再移动到下一个元素
        for current.Next != nil && current.Val == current.Next.Val {
            current.Next = current.Next.Next
        }
        current = current.Next
    }
    return head
}
```

### [remove-duplicates-from-sorted-list-ii](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)

> 给定一个排序链表，删除所有含有重复数字的节点，只保留原始链表中   没有重复出现的数字。

思路：链表头结点可能被删除，所以用 dummy node 辅助删除

```go
func deleteDuplicates(head *ListNode) *ListNode {
    if head == nil {
        return head
    }
    dummy := &ListNode{Val: 0}
    dummy.Next = head
    head = dummy

    var rmVal int
    for head.Next != nil && head.Next.Next != nil {
        if head.Next.Val == head.Next.Next.Val {
            // 记录已经删除的值，用于后续节点判断
            rmVal = head.Next.Val
            for head.Next != nil && head.Next.Val == rmVal  {
                head.Next = head.Next.Next
            }
        } else {
            head = head.Next
        }
    }
    return dummy.Next
}
```

注意点
• A->B->C 删除 B，A.next = C
• 删除用一个 Dummy Node 节点辅助（允许头节点可变）
• 访问 X.next 、X.value 一定要保证 X != nil

### [reverse-linked-list](https://leetcode-cn.com/problems/reverse-linked-list/)

> 反转一个单链表。

思路：用一个 prev 节点保存向前指针，temp 保存向后的临时指针

```go
func reverseList(head *ListNode) *ListNode {
    var prev *ListNode
    for head != nil {
        // 保存当前head.Next节点，防止重新赋值后被覆盖
        // 一轮之后状态：nil<-1 2->3->4
        //              prev   head
        temp := head.Next
        head.Next = prev
        // pre 移动
        prev = head
        // head 移动
        head = temp
    }
    return prev
}
```

### [reverse-linked-list-ii](https://leetcode-cn.com/problems/reverse-linked-list-ii/)

> 反转从位置  *m*  到  *n*  的链表。请使用一趟扫描完成反转。

思路：先遍历到 m 处，翻转，再拼接后续，注意指针处理

```go
func reverseBetween(head *ListNode, m int, n int) *ListNode {
    // 思路：先遍历到m处，翻转，再拼接后续，注意指针处理
    // 输入: 1->2->3->4->5->NULL, m = 2, n = 4
    if head == nil {
        return head
    }
    // 头部变化所以使用dummy node
    dummy := &ListNode{Val: 0}
    dummy.Next = head
    head = dummy
    // 最开始：0->1->2->3->4->5->nil
    var pre *ListNode
    var i = 0
    for i < m {
        pre = head
        head = head.Next
        i++
    }
    // 遍历之后： 1(pre)->2(head)->3->4->5->NULL
    // i = 1
    var j = i
    var next *ListNode
    // 用于中间节点连接
    var mid = head
    for head != nil && j <= n {
        // 第一次循环： 1 nil<-2 3->4->5->nil
        temp := head.Next
        head.Next = next
        next = head
        head = temp
        j++
    }
    // 循环需要执行四次
    // 循环结束：1 nil<-2<-3<-4 5(head)->nil
    pre.Next = next
    mid.Next = head
    return dummy.Next
}
```

### [merge-two-sorted-lists](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

> 将两个升序链表合并为一个新的升序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。

思路：通过 dummy node 链表，连接各个元素

```java
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if(l1==null && l2==null)
            return null;
        ListNode p1=l1;
        ListNode p2=l2;
        
        ListNode dummy=new ListNode(-1);
        ListNode p3=dummy;
        while(p1!=null && p2!=null)
        {
            if(p1.val<=p2.val)
            {
                p3.next=p1;
                p3=p3.next;
                p1=p1.next;
            }
            else
            {
                p3.next=p2;
                p3=p3.next;
                p2=p2.next;
            }
        }
        p3.next=p1==null?p2:p1;  //处理未结束的节点
        
        return dummy.next;
    }
```

### [partition-list](https://leetcode-cn.com/problems/partition-list/)

> 给定一个链表和一个特定值 x，对链表进行分隔，使得所有小于  *x*  的节点都在大于或等于  *x*  的节点之前。

思路：将一个链表变为两个链表，一个按顺序连接比*x*小的节点，一个按顺序连接大于等于*x*的节点。
```java
public ListNode partition(ListNode head, int x) {
        ListNode smallHead=new ListNode(0);
        ListNode small=smallHead;
        ListNode largeHead=new ListNode(0);
        ListNode large=largeHead;
        while(head!=null)
        {
            if(head.val<x)
            {
                small.next=head;
                small=small.next;
            }
            else
            {
                large.next=head;
                large=large.next;
            }
            head=head.next;
        }
        large.next=null;
        small.next=largeHead.next;
        return smallHead.next;
    }
```
```java   
将小的往前移动
public ListNode partition(ListNode head, int x) {
        ListNode dummy=new ListNode(x-1); //最开头的哑结点
        dummy.next=head;
        ListNode smaller=dummy; //指向所遍历到的最后一个小节点
        ListNode p=dummy;//

        while(p.next!=null)
        {
            if(p.next.val<x && p.val>=x) //需要将下一个小于的节点前移
            {
                ListNode nodeMove=p.next; //保存要移动的节点
                p.next=p.next.next;
                nodeMove.next=smaller.next;
                smaller.next=nodeMove;
                smaller=smaller.next;
            }
            else if(p.next.val<x) //移动smaller 
            {
                smaller=p.next;
                p=p.next;
            }
            else
            {
                p=p.next;
            }
        }
        return dummy.next;
    }
```

哑巴节点使用场景

> 当头节点不确定的时候，使用哑巴节点

### [sort-list](https://leetcode-cn.com/problems/sort-list/)

> 在  *O*(*n* log *n*) 时间复杂度和常数级空间复杂度下，对链表进行排序。

思路：归并排序，找中点和合并操作

```go
func sortList(head *ListNode) *ListNode {
    // 思路：归并排序，找中点和合并操作
    return mergeSort(head)
}
func findMiddle(head *ListNode) *ListNode {
    // 1->2->3->4->5
    slow := head
    fast := head.Next
    // 快指针先为nil
    for fast !=nil && fast.Next != nil {
        fast = fast.Next.Next
        slow = slow.Next
    }
    return slow
}
func mergeTwoLists(l1 *ListNode, l2 *ListNode) *ListNode {
    dummy := &ListNode{Val: 0}
    head := dummy
    for l1 != nil && l2 != nil {
        if l1.Val < l2.Val {
            head.Next = l1
            l1 = l1.Next
        } else {
            head.Next = l2
            l2 = l2.Next
        }
        head = head.Next
    }
    // 连接l1 未处理完节点
    for l1 != nil {
        head.Next = l1
        head = head.Next
        l1 = l1.Next
    }
    // 连接l2 未处理完节点
    for l2 != nil {
        head.Next = l2
        head = head.Next
        l2 = l2.Next
    }
    return dummy.Next
}
func mergeSort(head *ListNode) *ListNode {
    // 如果只有一个节点 直接就返回这个节点
    if head == nil || head.Next == nil{
        return head
    }
    // find middle
    middle := findMiddle(head)
    // 断开中间节点
    tail := middle.Next
    middle.Next = nil
    left := mergeSort(head)
    right := mergeSort(tail)
    result := mergeTwoLists(left, right)
    return result
}
```

注意点

- 快慢指针 判断 fast 及 fast.Next 是否为 nil 值
- 递归 mergeSort 需要断开中间节点
- 递归返回条件为 head 为 nil 或者 head.Next 为 nil


### [Middle of the Linked List](https://leetcode-cn.com/problems/middle-of-the-linked-list/)
找到中间节点
思路：快慢指针，一个进步2格，一个进步1格
public ListNode middleNode(ListNode head) {
        ListNode slow = head, fast = head;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }
这两种方式不同点在于，**一般用 fast=head.Next 较多**，因为这样可以知道中点的上一个节点，可以用来删除等操作。

- fast 如果初始化为 head.Next 则中点在 slow.Next
- fast 初始化为 head,则中点在 slow

### [reorder-list](https://leetcode-cn.com/problems/reorder-list/)

> 给定一个单链表  *L*：*L*→*L*→…→*L\_\_n*→*L*
> 将其重新排列后变为： *L*→*L\_\_n*→*L*→*L\_\_n*→*L*→*L\_\_n*→…

思路：找到中点断开，翻转后面部分，然后合并前后两个链表

```java
class Solution {
    public void reorderList(ListNode head) {
        if(head==null)
            return;
        ListNode middleNode=getMiddleNode(head);
        ListNode head2=middleNode.next;
        middleNode.next=null;
        head2=reserve(head2);
        ListNode head1=head.next;
        ListNode p=head;
        while(head1!=null && head2!=null)
        {
            p.next=head2;
            head2=head2.next;
            p=p.next;
            p.next=head1;
            head1=head1.next;
            p=p.next;
        }
        p.next= head1==null?head2:head1;
        

    }
    //返回中间节点
    public ListNode getMiddleNode(ListNode head)
    {
        ListNode fast=head;
        ListNode slow=head;
        while(fast!=null && fast.next!=null)
        {
            slow=slow.next;
            fast=fast.next.next;
        }
        return slow;

    }
    //链表倒序
    public ListNode reserve(ListNode head)
    {
        if(head==null || head.next==null)
            return head;
        ListNode newHead=reserve(head.next);
        head.next.next=head;
        head.next=null;
        return newHead;
        
    }

}
```

### [linked-list-cycle](https://leetcode-cn.com/problems/linked-list-cycle/)

> 给定一个链表，判断链表中是否有环。

思路：快慢指针，快慢指针相同则有环，证明：快指针会比慢指针先一步进入环中，当慢指针进入环中，可以看成是快指针去追慢指针，每经过一次循环，快指针离慢指针就近一步，所以如果有环则快指针一定会和慢指针相遇在一个位置。
![fast_slow_linked_list](https://img.fuiboom.com/img/fast_slow_linked_list.png)

```java
public boolean hasCycle(ListNode head) {
        if(head==null)
            return false;
        ListNode fast=head.next;
        ListNode slow=head;
        while(fast!=slow)
        {
            if(fast!=null && fast.next!=null)
            {
                fast=fast.next.next;
            }
            else
                return false;
            slow=slow.next;
            
        }
        return true;
    }
```

### [linked-list-cycle-ii](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

> 给定一个链表，返回链表开始入环的第一个节点。  如果链表无环，则返回  `null`。

思路：快慢指针，快慢相遇之后，慢/快指针回到头，快慢指针步调一致一起移动，相遇点即为入环点
![cycled_linked_list](https://img.fuiboom.com/img/cycled_linked_list.png)

```java
public ListNode detectCycle(ListNode head) {
        if(head==null)
            return null;
        ListNode fast=head;
        ListNode slow=head;
        while(true)
        {
            if(fast!=null && fast.next!=null)
            {
                fast=fast.next.next;
            }
            else
                return null;
            slow=slow.next;
            if(slow==fast)
                break;
        }
        fast=head;
        while(fast!=slow)
        {
            fast=fast.next;
            slow=slow.next;
        }
        return fast;
        
    }
```
证明： 假设fast走了f步，slow走了s步，f=2s；
	fast和slow在同一点相遇，此时，fast比slow多走了n个cycle，假设cycle的长度为c,则第一次相遇时，f=s+nc; 联立两个等式，得到s=nc，所以只要s再走a步（head到入口节点的距离），s就到了入口节点，所以让另一个节点从起点出发。
	
坑点

- 指针比较时直接比较对象，不要用值比较，链表中有可能存在重复值情况
- 第一次相交后，快指针需要从下一个节点开始和头指针一起匀速移动




### [palindrome-linked-list](https://leetcode-cn.com/problems/palindrome-linked-list/)

> 请判断一个链表是否为回文链表。
方法1：快慢指针：找到中间节点之后，将后半部分翻转，进行对比。实际使用中，应该在判断完之后将它恢复，此外，在并发环境下，函数运行时需要锁定其他线程或进程对链表的访问，因为在函数执行过程中链表会被修改
```java
public boolean isPalindrome(ListNode head) {
        if(head==null||head.next==null)
            return true;
        ListNode fast=head;
        ListNode slow=head;
        while(fast!=null && fast.next!=null)
        {
            slow=slow.next;
            fast=fast.next.next;
        }
        ListNode head2=reverse(slow);
        while(head!=null && head2!=null)
        {
            if(head.val!=head2.val)
                return false;
            head=head.next;
            head2=head2.next;
        }
        return true;

    }
    public ListNode reverse(ListNode head)
    {
        if(head==null|| head.next==null)
            return head;
        ListNode newHead=reverse(head.next);
        head.next.next=head;
        head.next=null;
        return newHead;
    }
```
方法2：递归
```java
class Solution {
    private ListNode frontPointer;

    private boolean recursivelyCheck(ListNode currentNode) {
        if (currentNode != null) {
            if (!recursivelyCheck(currentNode.next)) {
                return false;
            }
            if (currentNode.val != frontPointer.val) {
                return false;
            }
            frontPointer = frontPointer.next;
        }
        return true;
    }

    public boolean isPalindrome(ListNode head) {
        frontPointer = head;
        return recursivelyCheck(head);
    }
}


```

### [copy-list-with-random-pointer](https://leetcode-cn.com/problems/copy-list-with-random-pointer/)

> 给定一个链表，每个节点包含一个额外增加的随机指针，该指针可以指向链表中的任何节点或空节点。
> 要求返回这个链表的 深拷贝。

思路：1、hash 表存储指针，2、复制节点跟在原节点后面

```go
func copyRandomList(head *Node) *Node {
	if head == nil {
		return head
	}
	// 复制节点，紧挨到到后面
	// 1->2->3  ==>  1->1'->2->2'->3->3'
	cur := head
	for cur != nil {
		clone := &Node{Val: cur.Val, Next: cur.Next}
		temp := cur.Next
		cur.Next = clone
		cur = temp
	}
	// 处理random指针
	cur = head
	for cur != nil {
		if cur.Random != nil {
			cur.Next.Random = cur.Random.Next
		}
		cur = cur.Next.Next
	}
	// 分离两个链表
	cur = head
	cloneHead := cur.Next
	for cur != nil && cur.Next != nil {
		temp := cur.Next
		cur.Next = cur.Next.Next
		cur = temp
	}
	// 原始链表头：head 1->2->3
	// 克隆的链表头：cloneHead 1'->2'->3'
	return cloneHead
}
```

## 总结

链表必须要掌握的一些点，通过下面练习题，基本大部分的链表类的题目都是手到擒来~

- null/nil 异常处理
- dummy node 哑巴节点
- 快慢指针
- 插入一个节点到排序链表
- 从一个链表中移除一个节点
- 翻转链表
- 合并两个链表
- 找到链表的中间节点

## 练习

- [ ] [remove-duplicates-from-sorted-list](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)
- [ ] [remove-duplicates-from-sorted-list-ii](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)
- [ ] [reverse-linked-list](https://leetcode-cn.com/problems/reverse-linked-list/)
- [ ] [reverse-linked-list-ii](https://leetcode-cn.com/problems/reverse-linked-list-ii/)
- [ ] [merge-two-sorted-lists](https://leetcode-cn.com/problems/merge-two-sorted-lists/)
- [ ] [partition-list](https://leetcode-cn.com/problems/partition-list/)
- [ ] [sort-list](https://leetcode-cn.com/problems/sort-list/)
- [ ] [reorder-list](https://leetcode-cn.com/problems/reorder-list/)
- [ ] [linked-list-cycle](https://leetcode-cn.com/problems/linked-list-cycle/)
- [ ] [linked-list-cycle-ii](https://leetcode-cn.com/problems/linked-list-cycle-ii/)
- [ ] [palindrome-linked-list](https://leetcode-cn.com/problems/palindrome-linked-list/)
- [ ] [copy-list-with-random-pointer](https://leetcode-cn.com/problems/copy-list-with-random-pointer/)
