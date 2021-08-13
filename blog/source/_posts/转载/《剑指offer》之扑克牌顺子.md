layout: post
title: 《剑指offer》之扑克牌顺子
author: QuellanAn
categories: 
  - 算法
tags:
  - 《剑指offer》
  - 算法
---

# 前言
接着来刷一道简单的算法题

# 题目
LL今天心情特别好,因为他去买了一副扑克牌,发现里面居然有2个大王,2个小王(一副牌原本是54张^_^)...他随机从中抽出了5张牌,想测测自己的手气,看看能不能抽到顺子,如果抽到的话,他决定去买体育彩票,嘿嘿！！“红心A,黑桃3,小王,大王,方片5”,“Oh My God!”不是顺子.....LL不高兴了,他想了想,决定大\小 王可以看成任何数字,并且A看作1,J为11,Q为12,K为13。上面的5张牌就可以变成“1,2,3,4,5”(大小王分别看作2和4),“So Lucky!”。LL决定去买体育彩票啦。 现在,要求你使用这幅牌模拟上面的过程,然后告诉我们LL的运气如何， 如果牌能组成顺子就输出true，否则就输出false。为了方便起见,你可以认为大小王是0。

# 分析
就是一个数组，0表示大小王，可以当癞子，其他的最小是1，最大是13，求给出的这个数组能否组成顺子。

我们仔细想想一个正常的顺子，比如6，7，8，9，10.
那最大值和最小值差为4，并且不能重复。所以我们应该得出两个结论。
1，最大值和最小值相差小于等于4，为什么会小于4呢，因为有癞子0导致的。
2.除了癞子0以外，其他的数字不能重复。

所以根据上面的条件我们就可以写出算法了。
求出最小值，最大值。

重复数字怎么判断呢？
先判断是否为0，不为0，将这个数字作为脚标存到另一个数组中，并计数加1，从而判断是否重复。

# 解法
```
public boolean isContinuous(int [] numbers) {
        int count[]=new int[14];
        if(numbers==null|| numbers.length<1){
            return false;
        }
        int min=14;
        int max=0;
        for(int i=0;i<numbers.length;i++){
            if(numbers[i]==0){
                continue;
            }else if(count[numbers[i]]>0) {
           //如果存在重复的数字就返回false;
                return false;
            }else if(numbers[i]<min){
                    min=numbers[i];
            }else if(numbers[i]>max){
                    max=numbers[i];
            }
            count[numbers[i]]++;
        }
        if(max-min<5){
            return true;
        }
        return false;
    }
```

# 测试
main方法
```
public static void main(String[] args) {
        Solution solution=new Solution();
        int array[]={1,5,3,3,4,};
        System.out.println(solution.isContinuous(array));
    }
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200213145742670.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020021314504021.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)

# 番外
测试的时候发现牛客上的这道题测试用例不全。比如我最开始这样写的
```
for(int i=0;i<numbers.length;i++){
            if(numbers[i]==0){
                continue;
            }else if(numbers[i]==min||numbers[i]== max) {
             //如果存在重复的数字就返回false;
                return false;
            }else if(numbers[i]<min){
                    min=numbers[i];
            }else if(numbers[i]>max){
                    max=numbers[i];
            }
            count[numbers[i]]++;
        }
```
可以看到我通过
```
(numbers[i]==min||numbers[i]== max
```
来判断是否存在重复的数字，其实这是不正确的，比如我是上面的测试用例1,5,3,3,4就不满足上面的要求。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200213145701645.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
可以看到返回的是true,实际应该是false才对，算法不正确的，但是提交到牛客上竟然通过了，有点无语，也不敢问哈哈
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200213150031632.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
