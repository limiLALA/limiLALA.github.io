layout: post
title: 《剑指offer》之二叉树的深度
author: QuellanAn
categories: 
  - 算法
tags:
  - 《剑指offer》
  - 算法
---

# 前言
我们今天接着来看一道关于二叉树的算法题，关于二叉树的深度。

# 题目
输入一棵二叉树，求该树的深度。从根结点到叶结点依次经过的结点（含根、叶结点）形成树的一条路径，最长路径的长度为树的深度。

# 分析
求该树的深度，主要就是看最长路径。比如下图的深度为5，最长的路径为34，99，35，64，77
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200210190247954.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
那应该怎么做了？这里用递归，如果当前节点没有左右节点，就返回当前节点，如果有左右节点，就返回左右节点的，比较左节点和右节点的深度，谁的深度大就返回那个。这样就可以获得树的最大深度啦。


# 解法
```
public int TreeDepth(TreeNode root) {
        if(root==null){
            return 0;
        }

        int left=TreeDepth(root.left);
        int right=TreeDepth(root.right);
        if(left>right){
            return left+1;
        }
        return right+1;

    }
```
上面主要注意的是left+1 和right+1;为什么要加一呢，因为我们递归的出口是当前节点为null ,返回0，为1个节点的话返回1.

# 测试
测试main方法
```
public static void main(String[] args) {
        TreeNode root =new TreeNode(34);
        root.left=new TreeNode(23);
        root.right=new TreeNode(99);
        root.left.left=new TreeNode(1);
        root.left.right=new TreeNode(27);
        root.right.left=new TreeNode(35);
        root.right.left.right=new TreeNode(64);
        root.right.left.right.right=new TreeNode(77);
        TreeOperation.show(root);
        Solution solution= new Solution();
        System.out.println(solution.TreeDepth(root));
    }
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200210192011852.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200210192140598.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
