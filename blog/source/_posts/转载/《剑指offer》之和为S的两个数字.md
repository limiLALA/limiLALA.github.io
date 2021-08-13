layout: post
title: 《剑指offer》之和为S的两个数字
author: QuellanAn
categories: 
  - 算法
tags:
  - 《剑指offer》
  - 算法
---

# 前言
昨天刷的和为S的连续正数序列，用到的窗口双指针的方法很好用，今天刚好刷到一道类似的，也是用窗口双指针的，一次就过的感觉还不错。

# 题目
输入一个递增排序的数组和一个数字S，在数组中查找两个数，使得他们的和正好是S，如果有多对数字的和等于S，输出两个数的乘积最小的。

输出描述:
> 对应每个测试案例，输出两个数，小的先输出。

# 分析
输出两个数的乘积最小的。这句话的理解？
假设：若b>a,且存在，
a + b = s;
(a - m ) + (b + m) = s
则：(a - m )(b + m)=ab - (b-a)m - m*m < ab；说明外层的乘积更小
也就是说依然是左右夹逼法！！！

上面这些是用来说明还是窗口双指针的方法，
left=0;
right=array.length-1;
左右夹逼，两个的和大于sum,right--,小于sum,left++，等于sum,退出

# 解法
```
public ArrayList<Integer> FindNumbersWithSum(int [] array, int sum) {
        ArrayList<Integer> list=new ArrayList<>();

        if(array==null||array.length==0||sum<=0){
            return list;
        }

        int left=0;
        int right=array.length-1;
        while(left<right){
            int add=array[left]+array[right];
            if(add==sum){
                list.add(array[left]);
                list.add(array[right]);
                break;
            }else if(add<sum){
                left++;
            }else{
                right--;
            }
        }
        return list;
    }
```

# 测试
main 方法
```
public static void main(String[] args) {
        int [] array={1,2,3,4,5,6,7,8,9,10};
        Solution solution= new Solution();
        ArrayList<Integer> list=solution.FindNumbersWithSum(array,9);
        for(Integer tmp:list){
            System.out.print(tmp+"\t");
        }
    }
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200213111419494.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
