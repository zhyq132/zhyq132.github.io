---
title: "First Algorithm：bubble-sort"
date: 2017-04-22T15:56:47+08:00
tags: ["golang","Algorithm"]
author: "七哥"
categories: ["Golang","Algorithm"]
---

## 写在最前面
- 2011年毕业后，入行的敲门语言是宇宙第一语言php，一开始工作就讲究个产出，即意味着要拿出东西，所以一直不停的写业务逻辑，并没有把注意力留一丁点在更高端点的计算机基础知识上，比如“算法”。接触了Go，并在生产环境发布上线几个业务逻辑后(例如websockets服务，ps:先贴下删除敏感逻辑后的[代码](https://github.com/zhyq132/cst)，后面有时间贴出来详细说明)，觉得效率比肩`c`的`golang`如果仅仅用来做普通业务，有点杀鸡用牛刀的感觉，不如用来作更深入点的。。。在我的理解里，如果不熟悉算法、不了解数据结构，就没有核心竞争力，是无法在coding这个行业里走得更深入一点的，那么，既然要深入，那就用golang开始我从未亲自手写过的`算法`吧

## 第一个算法：冒泡排序
- show me the code
```
package sort

//BubbleSort 冒泡排序
func BubbleSort(arr []int) []int {
	arrLength := len(arr)
	for {
		nextSort := false                //nextSort 默认值为false，不进行下一次循环，知道第二层for发现需要进行下一次排序循环为止
		for i := 1; i < arrLength; i++ { // 从索引i为1的第二元素开始，进行对比
			if arr[i] < arr[i-1] { //如果i小于i-1
				tmp := arr[i] //则把较大的i-1对应的元素移动到右边，那么最后排序结果就是从左到右 ，依次增大（类似`气泡`从水底上浮至水面体积会变大一样）
				arr[i] = arr[i-1]
				arr[i-1] = tmp
				nextSort = true
			}
		}

		if nextSort == false { //nextSort为false，意味着不再需要进行下一次循环，break跳出
			break
		}
	}

	return arr
}
```