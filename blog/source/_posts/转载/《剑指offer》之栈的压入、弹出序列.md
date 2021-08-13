layout: post
title: 《剑指offer》之栈的压入、弹出序列
author: QuellanAn
categories: 
  - 算法
tags:
  - 《剑指offer》
  - 算法
---

# 前言
我们关于栈的题目，这两天做的还是挺多的，无非就是压栈出栈。

# 题目
输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否可能为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如序列1,2,3,4,5是某栈的压入顺序，序列4,5,3,2,1是该压栈序列对应的一个弹出序列，但4,3,5,1,2就不可能是该压栈序列的弹出序列。（注意：这两个序列的长度是相等的）

# 分析
输入两个数组，第一个是入栈的顺序，第二个是出栈的顺序，判断第二个数组出栈的顺序是否正确。
比如入栈：1，2，3，4，5
那出栈：5，4，3，2，1 可以
4，5，3，2，1也可以。
4，5，3，1，2就不行。
所以我们遍历第二个数组，找到在第一个数组找中的位置，这样在这之前的都先入栈，然后出栈。最后栈内容为空则表示，出栈的顺序是可以的，否则就不行。
具体例子说明，比较清楚。
比如list1：{1,2,3,4,5}
list2：｛4，5，3，2，1｝
先遍历数组list2
第一个为4，找到在list1 中的位置为3记录下来，将前面的数字都入栈。这时栈中的数据为1->2->3->4
然后出栈4，此时栈的数据为1->2->3。
第二个为5，找到在list1 中为4大于3 ，位置为4记录下来，所以将list1 中的3+1 到4的数据压入栈中，即将5压入栈，此时栈的数据为：1->2->3->5
然后出栈5，此时栈的数据为1->2->3
第三个为3，此时在list1 中的位置为2小于4，等于3，直接出栈。此时栈的数据为1->2
第四个为2，此时在list1 中的位置为1小于4，等于2，直接出栈。此时栈的数据为1
第四个为1，此时在list1 中的位置为0小于4，等于1，直接出栈。此时栈的数据为空
所以说明list2 是一种出栈方式。

# 解法
```
public boolean IsPopOrder(int [] pushA,int [] popA) {
        Stack<Integer> stack=new Stack<>();
        if(pushA==null||popA==null||popA.length==0||pushA.length==0){
            return false;
        }
        int temp=-1;
        for(int i=0;i<popA.length;i++){
            int j=0;
			//找到i 在list1 中的位置。
            while(j<popA.length &&pushA[j]!=popA[i]){
                j++;
            }
            //如果找不到，则返回false
            if(j==popA.length){
                return false;
            }

            while(temp<j){
                temp++;
                stack.push(pushA[temp]);
            }

			//如果相等，则出栈
            if(stack.peek()==popA[i]){
                stack.pop();
            }
        }
        //如果为空，为真
        if(stack.isEmpty()){
            return true;
        }
        return false;
    }
```

# 测试
main  方法
```
public static void main(String[] args) {
        int [] pushA={1,2,3,4,5};
        int [] popA={4,5,3,2,1};
        Solution solution=new Solution();
        boolean b=solution.IsPopOrder(pushA,popA);
        System.out.println(b);
    }
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200206160102966.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200206160123471.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
