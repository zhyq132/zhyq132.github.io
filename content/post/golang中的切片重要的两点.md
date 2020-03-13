---
title: "golang中的切片重要的两点"
date: 2017-10-26T15:42:27+08:00
tags: ["golang"]
author: "七哥"
categories: ["Golang"]
---

## 先举例子
```
package main

import

func main() {
	slice := []int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}

	fmt.Println(slice, cap(slice), len(slice))
	s1 := slice[1:2]
	fmt.Println(s1, cap(s1), len(s1))

	s2 := slice[2:3]
	//从slice中切一段
	//slice[2:3] 意味着从slice索引为2的地方开始切，切到slice的第三个数据处终止，那么s2的值就是[2]
	fmt.Println(s2, cap(s2), len(s2))

	s3 := slice[2:7]
	//从slice中切一段
	//同样的道理 从所因为2的元素处开始切，切刀第七个元素的终止，那么s3就是[2,3,4,5,6]
	fmt.Println(s3, cap(s3), len(s3))
}

```

## 划重点
1. [2:7]意味着从源数组的索引值=2的地方开始切，一直切到源数组的第七个元素。
2. 容量的计算方法：从源数组的索引值=2开始，知道源数组的最后一个元素，有多少个，新切片的容量就是多少