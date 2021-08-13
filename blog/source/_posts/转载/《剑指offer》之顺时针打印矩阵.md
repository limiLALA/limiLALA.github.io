layout: post
title: 《剑指offer》之顺时针打印矩阵
author: QuellanAn
categories: 
  - 算法
tags:
  - 《剑指offer》
  - 算法
---

# 前言
今天做一道关于矩阵的题目。思路很简单，就是要考虑全面。

# 题目
输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字，例如，如果输入如下4 X 4矩阵： 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 则依次打印出数字1,2,3,4,8,12,16,15,14,13,9,5,6,7,11,10.

# 分析
就是顺时针打印，不能重复打印元素。注意四个变量，上下左右。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200205114303573.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)

# 解法
我们先直接思路解题，代码不是很简洁，但是思路算是比较清晰，先判断数组是否为空。
```
public ArrayList<Integer> printMatrix(int [][] matrix) {
        if(matrix==null){
            return null;
        }
        ArrayList<Integer> list=new ArrayList<>();
        //设置四个边界值。
        int n=matrix.length;
        int m=matrix[0].length;
        int x=0;
        int y=0;
        while(n>x && m>y){
            int i=x;
            int j=y;
            //最外层从左往右输出
            while(j<m){
                list.add(matrix[i][j]);
                j++;
            }
            j--;
            i++;
            //最外层从上往下输出
            while(i<n){
                list.add(matrix[i][j]);
                i++;
            }
            i--;
            j--;
            //最外层从右往左输出，注意排除和前面的从左往右重复
            while(j>=y&&n!=x+1){
                list.add(matrix[i][j]);
                j--;
            }
            j++;
            i--;
            //最外层从下往上输出，注意排除和前面的从上往下重复
            while(i>x &&m!=y+1){
                list.add(matrix[i][j]);
                i--;
            }
            n--;
            m--;
            x++;
            y++;
        }
        return list;
    }
```

# 测试
main 方法
```
public static void main(String[] args) {
        int [][]matrix={{1,2,3,4,5},{6,7,8,9,10},{11,12,13,14,15}};
        //int [][]matrix={{1,2,3,4},{5,6,7,8},{9,10,11,12},{13,14,15,16},{17,18,19,20}};
        for(int i=0;i<matrix.length;i++){
            for (int j=0;j<matrix[0].length;j++){
                System.out.print(matrix[i][j]+"\t");
            }
            System.out.println();
        }
        Solution solution=new Solution();
        ArrayList<Integer> list=solution.printMatrix(matrix);
        for(int i=0;i<list.size();i++){
            System.out.print(list.get(i)+"\t");
        }

    }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020020511575325.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200205115848197.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9xdWVsbGFuYW4uYmxvZy5jc2RuLm5ldA==,size_16,color_FFFFFF,t_70)



