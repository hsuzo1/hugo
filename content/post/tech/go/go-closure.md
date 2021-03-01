---
title: "Go中闭包的简单使用"
date: 2021-03-01T20:18:56+08:00
draft: false
categories: ["tech"]
Tags : ["go"]
keywords: "go 闭包 匿名函数"
---

Github上的文章[the-way-to-go_ZH_CN](https://github.com/unknwon/the-way-to-go_ZH_CN/blob/master/eBook/06.8.md)对闭包的解释为：  
匿名函数同样被称之为闭包（函数式语言的术语）：它们被允许调用定义在其它环境下的变量。闭包可使得某个函数捕捉到一些外部状态，例如：函数被创建时的状态。另一种表示方式为：一个闭包继承了函数所声明时的作用域。这种状态（作用域内的变量）都被共享到闭包的环境中，因此这些变量可以在闭包中被操作，直到被销毁。  
CSDN上的说法：闭包是匿名函数与匿名函数所引用环境的组合。匿名函数有动态创建的特性，该特性使得匿名函数不用通过参数传递的方式，就可以直接引用外部的变量。这就类似于常规函数直接使用全局变量一样，个人理解为：匿名函数和它引用的变量以及环境，类似常规函数引用全局变量处于一个包的环境。  

### 且看代码：
```go
package main

import "fmt"

func main() {
	var f = adder()
	fmt.Print(f(1), " - ")
	fmt.Print(f(20), " - ")
	fmt.Print(f(300))

	fb := fib()
    for i := 0; i < 10; i++ {
        fmt.Println(fb())
    }
}

func adder() func(int) int {
	var x int
	return func(delta int) int {
		x += delta
		return x
	}
}

func fib() func() int {
    var a int = 0
    var b int = 1
    return func() int {
        c := a
        a = b
        b = a + c
        return c
    }
}
```
运行结果如下：
```shell
1 - 21 - 3210
1
1
2
3
5
8
13
21
34
```

上面函数中，每调用一次 **f**，系统就执行一次**func(delta int) int**，而**func adder()**函数中的**x**的值也被更新一次。第二个斐波那契数列函数也是如此，每循环一次，函数**fb()**就执行一次，执行时将内部数值和外部函数的**a、b**更新（相当于暂存起来），每次执行后就返回前面累加后的结果**c**。