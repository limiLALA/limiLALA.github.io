layout: post
title: 《剑指offer》之和为S的连续正数序列
author: QuellanAn
categories: 
  - 算法
tags:
  - 《剑指offer》
  - 算法
---

# 前言
今天刷的一道题目是是关于穷举的。

# 题目
小明很喜欢数学,有一天他在做数学作业时,要求计算出9~16的和,他马上就写出了正确答案是100。但是他并不满足于此,他在想究竟有多少种连续的正数序列的和为100(至少包括两个数)。没多久,他就得到另一组连续正数和为100的序列:18,19,20,21,22。现在把问题交给你,你能不能也很快的找出所有和为S的连续正数序列? Good Luck!

输出描述:
> 输出所有和为S的连续正数序列。序列内按照从小至大的顺序，序列间按照开始数字从小到大的顺序

# 分析
这里参考了答案区的思路，这个思路算是很厉害的，窗口的双指针的思路。窗口的左右两边就是两个指针，我们根据窗口内值之和来确定窗口的位置和宽度。
以sum=15为例吧，
初始left=1;right=2;和add=1+2=3;
add<sum，所以right++;
所以add=1+2+3=6;
还是add<sum,right++
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200212120010448.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
到right=5时，
add=1+2+3+4+5=15;刚好等于15，所以这一组是我们求的。我们继续
left++,
所以此时left=2,right=5;
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200212121607814.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
此时add=2+3+4+5=14;
add<sum；所以right++;
此时right=6,add=2+3+4+5+6=20;
add>sum，所以left++,
此时left=3,add=3+4+5+6=18;
add>sum，所以left++,
此时left=4,add=4+5+6=15;
add=sum ，将这组记录下来，继续left++;
此时left=5,rigt=6;add=5+6=11;
add<sum；所以right++;
此时right=7,add=5+6+7=21;
add>sum，所以left++,
此时left=6,rigt=7;add=6+7=14;
add<sum；所以right++;
此时left=6,rigt=8;add=6+7+8=21;
add>sum，所以left++,
此时left=7,rigt=8;add=7+8=21;
add=sum ，将这组记录下来，继续left++;
此时left=8,rigt=8;left>=right，循环结束退出，这时我们就可以得到三组满足条件的序列啦。

通过上面的例子，思路算是非常清楚了，主要是左右两个指针，当left<right 就执行循环体。循环体做的事情就是，先求left加到right 的和。
```
add=(left+right)*(right-left+1)/2; 
```
这个求和很好算吧，等差数列就和
```
add=(a1+an)*n/2
a1=left;
an=right;
n=right-left+1;
代替上去就可以了。
```
然后就比较add 与sum 的大小，如果add< sum ，则右指针right右移一位，如果add>sum，则左指针右移一位，如果add=sum,就表明该组是我们想要的，记录下来，同时l左指针右移一位,继续查找其他。
当left>=right的时候就退出循环体。

# 解法
```
public ArrayList<ArrayList<Integer>> FindContinuousSequence(int sum) {
        ArrayList<ArrayList<Integer> >  lists = new ArrayList<> ();
        int left=1;
        int right=2;
        while(left<right){
            int add=(left+right)*(right-left+1)/2;
            if(add==sum){
                ArrayList<Integer> list=new ArrayList<>();
                for(int i=left;i<=right;i++){
                    list.add(i);
                }
                lists.add(list);
                left++;
            }else if(add<sum){
                right++;
            }else {
                left++;
            }
        }
        return lists;
    }
```

# 测试
main 方法
```
public static void main(String[] args) {
        Solution solution= new Solution();
        ArrayList<ArrayList<Integer>> lists=solution.FindContinuousSequence(15);
        for(ArrayList<Integer> list:lists){
            for(Integer tmp:list){
                System.out.print(tmp+"\t");
            }
            System.out.println();
        }
    }
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200212123752157.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
再测一个100的
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200212123819814.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200212123857412.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3NzkwMDEx,size_16,color_FFFFFF,t_70)
