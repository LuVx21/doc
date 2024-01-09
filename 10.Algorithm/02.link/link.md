<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [中间分割链表为2](#中间分割链表为2)
- [交叉合并2个链表](#交叉合并2个链表)

<!-- /TOC -->
</details>


## 中间分割链表为2
```Java
/**
 * 1->2->3->4->5->6->7
 * ↓
 * 1->2->3->4
 * 5->6->7
 */
public static ListNode[] filterLink(ListNode head) {
    if (head == null || head.next == null) {
        return new ListNode[]{head, null};
    }
    ListNode fast = head, slow = head;
    while (fast != null && fast.next != null) {
        fast = fast.next.next;
        ListNode next = slow.next;
        if (fast == null) {
            slow.next = null;
        }
        slow = next;
        if (fast != null && fast.next == null) {
            ListNode next1 = slow.next;
            slow.next = null;
            slow = next1;
        }
    }

    return new ListNode[]{head, slow};
}
```

方式2:

```Java
/**
    * 1->2->3->4->5->6->7
    * ↓
    * 1->2->3
    * 4->5->6->7
    */
public static ListNode[] filterLink(ListNode head) {
    if (head == null || head.next == null) {
        return new ListNode[]{head, null, null};
    }
    ListNode pre = head, slow = pre.next, fast = slow.next;
    //找到链表的中点p
    while (fast != null && fast.next != null) {
        pre = pre.next;
        slow = pre.next;
        fast = fast.next.next;
    }
    pre.next = null;
    return new ListNode[]{head, pre, slow};
}
```

## 交叉合并2个链表

```Java
/**
* 1->3->5->7
* 2->4->6->8
* ↓
* 1->2->3->4->5->6->7->8
 */
private void merge(ListNode head1, ListNode head2) {
    if (head2 == null) {
        return;
    }
    ListNode current = head1, temp = head2;
    while (temp != null) {
        ListNode head1Next = head1.next;
        ListNode head2Next = head2.next;
        current.next = temp;
        current = current.next;
        if (temp == head1) {
            head2 = temp = head2Next;
        } else {
            head1 = temp = head1Next;
        }
    }
}
```



