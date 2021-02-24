---
title: "Go語言初見"
date: 2021-02-24T20:20:27+08:00
draft: false
categories: ["tech"]
Tags : ["golang"]
cover : "img/tech/golangfirst (Small).jpg"
---

   近期偶然對[Go](https://golang.google.cn/)語言產生了興趣，於是初略學習了一下，感覺確實非常犀利。  
Go 于 2009 年由谷歌开发完成。它解决了一个问题，创建出一种没有 C++等语言的过重包袱和过分设计的语言。
这使得 Go 在性能和速度上都有极大提升，使用 Go 工作成为一件乐事。  
此外，大部分第一次使用 Go 语言的开发者不会感觉到不便，因为 Go 中的熟悉元素及其易用性构成了一项惊喜。这并不是说 Go 语言在所有情况下都是完美的。
虽然它将速度和优雅度提升到新的层次，但也舍弃了一些用户期望的功能。比如，它不具备大量库，也不支持继承。此外，它没有 GUI 库，也不支持对象导向的编程。Go 语言的特点表明它具备轻量级线程实现（Goroutine）、智能标准库、强大的内置安全性，且可使用最简语法进行编程。
> 官網上的介紹：  
> Go is an open source programming language that makes it easy to build simple, reliable, and efficient software. 

> 知乎上有文章進行了Python和Go詳細對比分析，感興趣的可以[查看原文](https://zhuanlan.zhihu.com/p/107578421)。

### 小測試

編寫了一個計算1w以内素數的脚步，對比Python和Go的耗時情況。  
- **Go脚本**，運行基本在 **50** 毫秒左右。
```go
package prime

import (
	"fmt"
	"time"
)

// GetPrime 用于计算素数
func GetPrime() int {
	fmt.Println("start")
	/*
   	素数：打印2-100内的素数。只能被2和本身整除。
    */
	t := time.Now()
	countsum := 0
	for i := 2; i <= 10000; i++ {
		count := 0 //用于统计i被整除的次数
		for j := 2; j < i; j++ {
		   if i%j == 0 {
			  count++
			  break
		   }
		}
		// fmt.Println(count)
		if count == 0 {
		//    fmt.Println(i, "是素数")
		countsum++
		}
	 }
	elapsed := time.Since(t)
    fmt.Println("app elapsed:", elapsed)
	return countsum
}
```  
  
- **Python脚本**，運行基本在 **660** 毫秒左右。
```python
# -*- coding:utf-8 -*-
'''
求 n 以内的素数个数，并获得耗时
'''

import datetime
import time

t1 = datetime.datetime.now().microsecond
t3 = time.mktime(datetime.datetime.now().timetuple())

prm_lst = []

def get_prime(num0):
    rst = False
    if num0<=1:
        # print('这不是质数')
        rst = False
    elif num0==2:
        # print('这是一个质数!')
        rst = True
    else:
        i=2
        while i<num0:
            if num0%i==0:
                # print('这不是一个质数')
                break
            i=i+1
        else:
            # print ('这是一个质数!')
            rst = True
    return rst

for i in range(0, 10000):
    if get_prime(i):
        prm_lst.append(i)


t2 = datetime.datetime.now().microsecond
t4 = time.mktime(datetime.datetime.now().timetuple())
strTime = 'funtion time use:%dms' % ((t4 - t3) * 1000 + (t2 - t1) / 1000)
print(len(prm_lst), strTime)

```

## 總結
以上對比可見，個人覺得 Go 性能確實優越（本人業餘水平，測試不一定專業嚴謹）。而且在計算100w級別的時候二者差異更是明顯。  
我此前學習的是Delphi，後來自學Python，現在來學習Go語言，覺得一些基本感念比較容易理解。以後有時間再深入學習吧。