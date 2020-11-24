---
title: "辅导小学奥数时必须掌握的整除知识"
date: 2020-11-24T09:14:23+08:00
draft: false
categories: ["life"]
Tags : ["math", "整除", "小学", "奥数"]
description : 小学生家长的必备知识点，整数被2、5、7、9、11、13等整除的判断技巧。
keywords : "小学 奥数 整除 7、11、13 数论 "
Math : True
---
  

### 一、基本概念和符号：

1、整除：如果一个整数a，除以一个自然数b，得到一个整数商c，而且没有余数，那么叫做a能被b整除或b能整除a，记作**b|a**。

2、常用符号：整除符号“**|**”，不能整除符号“”;因为符号“**∵**”，所以的符号“**∴**”;
  

### 二、整除判断方法：

1. 能被**2、5**整除：末位上的数字能被**2、5**整除。  
2. 能被**4、25**整除：末两位的数字所组成的数能被**4、25**整除。  
3. 能被**8、125**整除：末三位的数字所组成的数能被**8、125**整除。  
$$ 也就是说，一个整数能否被2^x 或者 5^x 整除，就看这个整数的后x位能否被2或5整除。$$
4. 能被**3、9**整除：各个数位上数字的和能被**3、9**整除。  
5. 能被**7**整除：  
①**末三位**上数字所组成的数与**末三位以前**的数字所组成数之差能被**7**整除。  
②逐次去掉最后一位数字并减去末位数字的**2倍**后能被**7**整除。

6. 能被**11**整除：  
①**末三位**上数字所组成的数与**末三位**以前的数字所组成的数之差能被11整除。  
②奇数位上的数字和与偶数位数的数字和的差能被11整除。  
③逐次去掉最后一位数字并减去末位数字后能被11整除。

7. 能被**13**整除：  
①末三位上数字所组成的数与末三位以前的数字所组成的数之差能被13整除。  
②逐次去掉最后一位数字并减去末位数字的9倍后能被13整除。

### 三、整除的性质：

1. 如果a、b能被c整除，那么 **(a+b)** 与 **(a-b)** 也能被c整除。  
2. 如果a能被b整除，c是整数，那么a乘以c也能被b整除。  
3. 如果a能被b整除，b又能被c整除，那么a也能被c整除。  
4. 如果a能被b、c整除，那么a也能被b和c的**最小公倍数**整除。
  


### 四、数的整除性练习题

#### A.数的整除性规律练习

- 【能被2或5整除的数的特征】一个数的末位能被2或5整除，这个数就能被2或5整除  
- 【能被3或9整除的数的特征】一个数，当且仅当它的各个数位上的数字之和能被3和9整除时，这个数便能被3或9整除。
1. 例如，1248621各位上的数字之和是1+2+4+8+6+2+1=24  
3|24，则3|1248621。
2. 又如，372681各位上的数字之和是3+7+2+6+8+1=27  
9|27，则9|372681。

- 【能被4或25整除的数的特征】一个数，当且仅当它的末两位数能被4或25整除时，这个数便能被4或25整除。
1. 整数173824的末两位数为24，4|24，则4|173824。
2. 整数43586775的末两位数为75，25|75，则25|43586775。

- 【能被8或125整除的数的特征】一个数，当且仅当它的末三位数字为0，或者末三位数能被8或125整除时，这个数便能被8或125整除。
1. 整数32178000的末三位数字为0，则这个数能被8整除，也能够被125整除。
2. 3569824的末三位数为824，8|824，则8|3569824。
3. 214813750的末三位数为750，125|750，则125|214813750。

- 【能被7、11、13整除的数的特征】一个数，当且仅当它的末三位数字所表示的数，与末三位以前的数字所表示的数的差(大减小的差)能被7、11、13整除时，这个数就能被7、11、13整除。
1. 例如，75523的末三位数为523，末三位以前的数字所表示的数是75，523-75=448，448÷7=64，即7|448，则7|75523。
2. 又如，1095874的末三位数为874，末三位以前的数字所表示的数是1095，1095-874=221，221÷13=17，即13|221，则13|1095874。
3. 再如，868967的末三位数为967，末三位以前的数字所表示的数是868，967-868=99，99÷11=9，即11|99，则11|868967。

**此外，能被11整除的数的特征，还可以这样叙述：一个数，当且仅当它的奇数位上数字之和，与偶数位上数字之和的差(大减小)能被11整除时，则这个数便能被11整除。**
1. 例如，4239235的奇数位上的数字之和为4+3+2+5=14，偶数位上数字之和为2+9+3=14，二者之差为14-14=0，0÷11=0，即11|0，则11|4239235。

#### B.数的整除运用方法

- **位差法**——把一个数由右边向左边数，将奇位上的数字与偶位上的数字分别加起来，再求它们的差，如果这个差是11的倍数(包括0)，那么，原来这个数就一定能被11整除。  
**例如**:  
判断491678能不能被11整除。  
—→**奇位**数字的和9+6+8=23  
—→**偶位**数位的和4+1+7=1223-12=11  
因此，491678能被11整除。  
这种方法叫"**奇偶位差法**"。

- **割减法**——从一个数里减去11的10倍、20倍、30倍……到余下一个100以内的数为止。如果这个余数能被11整除，那么，原来这个数就一定能被11整除。  
**例如**：  
判断583能不能被11整除。  
用583减去11的50倍(583-11×50=33)余数是33，33能被11整除，所以583也一定能被11整除。


>1. 1与0的特性：  
1是任何整数的约数，即对于任何整数a，总有**1|a**.  
0是任何非零整数的倍数，**a≠0**，a为整数，则**a|0**.  
>2. 若一个整数能被2和3整除，则这个数能被6整除。

> 原文网址：https://www.51test.net/show/9197799.html
