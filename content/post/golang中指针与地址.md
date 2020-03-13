---
title: "golang中的*与&"
date: 2017-10-25T17:49:30+08:00
tags: ["golang"]
author: "七哥"
categories: ["Golang"]
---

## 先举例子
```
package main

import

func main() {
	var a int = 1
	var b *int = a
	c:= *b
	
	fmt.Println(a,b,c)
}

//打印结果：1 0xc420066018 1
```

## 讲解上述例子
1. 变量a的类型为int
2. 变量b的类型是指针类型，*int代表：指向int类型的指针
3. &a表示，对变量a取地址
4. *b表示，对指针取值，也可以说是对（内存）地址取值


> ps:3、 4 点很重要，理解好了你尝试答一下下面的题目,你知道我为啥要用---把打印的结果分开来吗

## 测试

```
package main

import

func main() {
	var a int = 1
	var b *int = a
	var c **int = b
	var x int = *b
	fmt.Println(a = , a)
	fmt.Println(*a = , *a)
	fmt.Println(x = , x)
	fmt.Println(*b = , *b)
	fmt.Println(**c = , **c)
	fmt.Println(******c = , *******c)

	fmt.Println(-----------------)

	fmt.Println(a = , a)
	fmt.Println(b = , b)
	fmt.Println(*b = , *b)
	fmt.Println(*c = , *c)

	fmt.Println(-----------------)

	fmt.Println(b = , b)
	fmt.Println(c = , c)
	fmt.Println(*c = , *c)

	fmt.Println(-----------------)

	fmt.Println(c = , c)
}
```