---
title: "golang-Excel中列名换算：（2）根据列名计算对应的数字"
date: 2017-11-07T16:36:29+08:00
tags: ["golang"]
author: "七哥"
categories: ["Golang"]
---


紧接上文，我们思考下，现在数字-》字符，我们可以了，但是根据英文字符，能否计算出数字呢？

## 思路
> 数字->字符，我们可以，理所应当我们可以算出来字符-》数字，依据还是进制的转换：26进制-》十进制

## 还是先看代码
```
package main

import (
	fmt
	math
)

func main() {
	x := Z
	y := AZ
	z := AAC

	var str int
	str = GetNumByLetter(x)
	fmt.Println(str)
	str = GetNumByLetter(y)
	fmt.Println(str)
	str = GetNumByLetter(z)
	fmt.Println(str)
	//打印结果如下：
	//26
    //52
    //705
}

func GetNumByLetter(s string) (num int) {
	r := []rune(s)

	for i := len(r) - 1; i = 0; i-- {
		num += int(math.Pow(float64(26), float64(i))) * int(r[len(r)-i-1]-A+1)
	}
	return
}
```

## 位值原理
- [点击了解](http://www.jzb.com/bbs/thread-2114557-1-1.html)

## 利用位值原理进行八进制->十进制的转换
```
八进制：7654
7-8^3*7=  3584
           +
6-8^2*6=  384
           +
5-8^1*5=  40
           +
4-8^0*4=  4
        =  4012
```
- 即是：进制为底数，从右到左的`位数-1`为指数做幂运算，乘以当前位的值，既是当前`位`代表的十进制值，每一位相加，得出十进制的

## 同理26进制的运算
- 我们把A-Z的26个字符，当成26进制中的表示符号
```
26进制：Z
Z-26^0*Z=1*Z
上面我们定义了z表示26，那么26进制的Z就表示十进制的26

```
- 好了，上面的转换没有问题，那么我们接着来看一下更复杂点的AAC的转换 
```
按照我们上述的思路，26进制：AZ 应该如下所示
A-26^2*A= 676
           +
A-26^1*A= 26
           +
C-26^0*Z= 3
          =705
```

## 上述问题是否有解决你的疑问？如果理解上述算法的话， 试着解一下ZZZQ对应的值

