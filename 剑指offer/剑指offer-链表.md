---
title: 链表面试题
date: 2018-05-01 13:13:26
tags: [剑指offer]
categories: [学习,编程]
---
###剑指offer题目分类
#####2、链表
面试题6：从尾到头打印链表
```java
import java.util.ArrayList;
public class Solution {
	ArrayList<Integer> res = new ArrayList<Integer>();
    public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
        if(listNode == null) return res;
        printListFromTailToHead(listNode.next);
        res.add(listNode.val);
        return res;
    }
}
```

面试题22：链表中倒数第k个节点
```java
public class Solution {
    public ListNode FindKthToTail(ListNode head,int k) {
    //利用链表本身的遍历进行计数：
    //先遍历进行k-1次，到达第k个结点，然后一个从k开始遍历，另一个从头结点开始遍历，
    //返回当第一个遍历结束时，第二个所在的位置，即为倒数第k个结点
        if(head == null || k <= 0) return null;
        ListNode pre = head, last = head;
        for(int i = 1; i < k; i++){
            if(pre.next != null) pre = pre.next;
            else return null;
        }
        while(pre.next != null){
            pre = pre.next;
            last = last.next;
        }
        return last;
    }
}
```

面试题24：反转链表
```java

```