---
title: "select-sort"
date: 2017-04-23T21:18:42+08:00
tags: ["golang","Algorithm"]
author: "七哥"
categories: ["Golang","Algorithm"]
---

## 选择排序
- show me the code
```
package sort

//SelectSort 选择排序
func SelectSort(arr []int) []int {
	arrLength := len(arr)

	var limitIndex int //定义一个参数，用来保存当次循环中的最小值的索引
	for i := 0; i < arrLength; i++ {
		limitIndex = i
		for j := i + 1; j < arrLength; j++ {
			if arr[j] < arr[limitIndex] { //如果j对应的值不是最小的，那么就把j赋值给代表最小值索引的参数：limitIndex
				limitIndex = j
			}
		}

		if i != limitIndex { //i与limitIndex不一样，则交换位置
			temp := arr[i]
			arr[i] = arr[limitIndex]
			arr[limitIndex] = temp
		}
	}

	return arr
}

```