layout: post
title: 《剑指offer》之反转链表
author: QuellanAn
categories: 
  - 算法
tags:
  - 《剑指offer》
  - 算法
---

# 前言
今天又刷了一道关于链表的，本来打算接着前面一篇关于链表的写，但是想想还是再起一篇。

# 题目
输入一个链表，反转链表后，输出新链表的表头。

# 分析
最开始我以为输出表头的值，直接找到链表结尾的元素输出就好了，后来发现是输出表头。这就要从新思考了。需要反转链表，比如我们现在的链表为1->3->5->8 我们要反转成8->5->3->1

也就是说之前1的下一个节点是3，要锋、改成下一个节点是null
3的下一个节点是5，改成下一个节点是1.

这里我们遍历链表，listNode.next的节点先保存起来，然后将listNode.next的指向改成pre.

# 解法
```
public ListNode ReverseList(ListNode head) {
        if(head==null){
            return null;
        }
        ListNode listNode=head;
        ListNode pre=null;
        while(listNode!=null){
            ListNode next=listNode.next;
            listNode.next=pre;

            pre=listNode;
            listNode=next;
        }

        return pre;
    }
```

可以看到代码很简短，就是遍历listNode。将listNode.next 先保存到next 中，因为接着遍历会用到。
```
ListNode next=listNode.next;
```
然后listNode.next。
然后将当前节点保存到pre 中，用于下个节点的前节点。
最后将 next赋给listNode，用于执行下个节点。

# 源代码
```
public ListNode ReverseList(ListNode head) {
        if(head==null){
            return null;
        }
        ListNode listNode=head;
        ListNode pre=null;
        while(listNode!=null){
            ListNode next=listNode.next;
            listNode.next=pre;

            pre=listNode;
            listNode=next;
        }

        return pre;
    }


    public static void main(String[] args) {
        ListNode listNode=new ListNode(1);
        listNode.next=new ListNode(3);
        listNode.next.next=new ListNode(5);
        listNode.next.next.next=new ListNode(8);

        Solution solution=new Solution();
        ListNode dd=solution.ReverseList(listNode);
        System.out.println(dd.val);
    }
```

# 测试
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200203183118507.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200203183204606.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)

# 合并两个排序的链表
输入两个单调递增的链表，输出两个链表合成后的链表，当然我们需要合成后的链表满足单调不减规则。

## 分析
把两个有序的链表整合成一个有序的列表。
思路其实也很简单。比如我们两个链表为：
list1:1->3->5->8
list2: 2->4->6->7
先比较1和2
1小于2，则list1.next
在比较3和2
3大于2，则list2.next
直到整个链表都是有序的。

# 解法1
使用递归，我们返回的是是一个有序的链表
```
public ListNode Merge(ListNode list1, ListNode list2) {

        if(list1==null){
            return list2;
        }else if(list2==null){
            return list1;
        }

        if(list1.val<list2.val){
            list1.next=Merge(list1.next,list2);
            return list1;
        }else {
            list2.next=Merge(list1,list2.next);
            return list2;
        }
    }
```

# 解法2 
使用循环
```
public ListNode Merge2(ListNode list1, ListNode list2) {

        if(list1==null){
            return list2;
        }else if(list2==null){
            return list1;
        }
        ListNode head=new ListNode(-1);
        ListNode root=head;
        while(list1!=null&&list2!=null){
            if(list1.val<list2.val){
                head.next=list1;
                head=list1;
                list1=list1.next;
            }else{
                head.next=list2;
                head=list2;
                list2=list2.next;
            }
        }
        if(list1!=null){
            head.next=list1;
        }
        if(list2!=null){
            head.next=list2;
        }
        return root.next;
    }
```

# 主方法
```
public static void main(String[] args) {
        ListNode list1=new ListNode(1);
        list1.next=new ListNode(3);
        list1.next.next=new ListNode(5);
        list1.next.next.next=new ListNode(8);

        ListNode list2=new ListNode(2);
        list2.next=new ListNode(4);
        list2.next.next=new ListNode(6);
        list2.next.next.next=new ListNode(7);

        Solution solution=new Solution();
        ListNode dd=solution.Merge2(list1,list2);
        System.out.println(dd.val);
    }
```
