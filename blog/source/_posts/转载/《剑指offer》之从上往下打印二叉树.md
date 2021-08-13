layout: post
title: 《剑指offer》之从上往下打印二叉树
author: QuellanAn
categories: 
  - 算法
tags:
  - 《剑指offer》
  - 算法
---

# 前言
从上往下打印二叉树，这里会用到队列，所以先将一下Java队列。

# 队列
创建队列
```
Queue<String> queue = new LinkedList<String>();
```

添加元素
```
 queue.offer("a");
```

出队列
```
//返回第一个元素，并在队列中删除
queue.poll()

//返回队列头部的元素,如果队列为空，则抛出一个NoSuchElementException异常
queue.element()

//返回队列头部的元素, 如果队列为空，则返回null
queue.peek()
```
主要可能就用到这几个方法啦。下面来看题目

# 题目
从上往下打印出二叉树的每个节点，同层节点从左至右打印。

# 分析
打印一颗二叉树，如果直接遍历打印的话，回先打印根节点->左节点->右节点。
想要按层次打印，可以依照队列来实现，从根节点依次将节点加入队列中，然后从队列中取出来达到层次打印的目的。

#  解法
```
ArrayList<Integer> list=new ArrayList<>();
        if(root==null){
            return list;
        }
        Queue<TreeNode> queue=new LinkedList<>();
        queue.offer(root);
        while (!queue.isEmpty()){
            TreeNode temp=queue.poll();
            list.add(temp.val);
            if(temp.left!=null){
                queue.offer(temp.left);
            }
            if(temp.right!=null){
                 queue.offer(temp.right);
            }
        }
        return list;
```

# 测试
main 方法
```
public static void main(String[] args) {
        TreeNode root =new TreeNode(1);
        root.left=new TreeNode(2);
        root.right=new TreeNode(3);
        root.left.left=new TreeNode(4);
        root.right.left=new TreeNode(5);
        root.left.left.left=new TreeNode(6);
        TreeOperation.show(root);
        Solution solution= new Solution();
        ArrayList<Integer> list=solution.PrintFromTopToBottom(root);
        for(int i=0;i<list.size();i++){
            System.out.print(list.get(i)+"\t");
        }
    }
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200207145226845.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200207145248115.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)
