---
title: "golang中json的使用"
date: 2017-09-01T11:01:08+08:00
tags: ["golang"]
author: "七哥"
categories: ["Golang"]
---

[点击查看官方文档](https://blog.golang.org/json-and-go)
### 示例json字符串
- `{"Name":"Wednesday","Age":6,"Parents":["Gomez","Morticia"]}`
- `{"Name":"Wednesday","Age":6,"boolenValue":true,"Parents":["Gomez","Morticia"],"Bar":"asdfasdf"}`

### 固定格式json的使用
- golang 中固定格式的json使用，相对php来说比较死板，但是这也是动态语言、与脚本语言的区别,话不多说，请看go是如何处理上述json字符串的
- 固定格式的json，需要先顶一个struct，其中的字段要与json中的key一一对应，类型也要一一对应，不然解析会报错。相对于php来说，这个是不是有点繁琐？哪像php直接jsonDecode就解决问题

```
package main

import (
"encoding/json"
"fmt"
)

type Example struct {
Name string
Age int
Parents []string
}

var jsonString string = `{"Name":"Wednesday","Age":6,"Parents":["Gomez","Morticia"]}`

//var multiTypeJsonString = `{"Name":"Wednesday","Age":6,"boolenValue":true,"Parents":["Gomez","Morticia"],"Bar":"asdfasdf"}`

func main() {
b := []byte(jsonString)

var m Example
_ = json.Unmarshal(b, m)

fmt.Printf("%v", m)
}
```

### 不定结构的json字符串解析（Decoding arbitrary data）
- 不定结构，相对于实际使用情况来说，会用的比较多，因为多数php写的接口，其中数据的结构都不会很稳定，所以这种方式在实际应用中，会用到很多
- 这种方式重点有三个，1：json.Unmarshal解码json字符串后，需要类型断言才能进行下一步的使用；2：理解switch遍历去分辨值类型；3：断言后的类型与json的对应关系 `JSON的布尔型对应go的bool型,JSON数值型对应go的float64型,JSON的字符串型对应go的string型,JSON的 null类型对应go的nil型`
```
package main

import (
"encoding/json"
"fmt"
)

var multiTypeJsonString = `{"Name":"Wednesday","Age":6,"boolenValue":true,"Parents":["Gomez","Morticia"],"Parents2":{"objectKey":"Gomezdddd","objectKey2":"Morticiaaaaa"}}`

func main() {
b := []byte(multiTypeJsonString)

var m interface{}
err := json.Unmarshal(b, m)
if err != nil {
fmt.Printf("%v", err.Error())
}
fmt.Printf("%v \n", m)

// 类型断言后，可访问数据
f := m.(map[string]interface{})

//读取空接口中存储的decode后的json数据
for k, v := range f {
switch vv := v.(type) {
case float64:
fmt.Printf("%v 是数值型，值是：%v \n", k, vv)
case string:
fmt.Printf("%v 是字符型，值是：%v \n", k, vv)
case bool:
fmt.Printf("%v 是布尔型，值是：%v \n", k, vv)
case []interface{}:
fmt.Printf("%v 是json Array类型，值是：%v \n", k, vv)
case map[string]interface{}:
fmt.Printf("%v 是json object类型，值是：%v \n", k, vv)
default:
fmt.Printf("%v的值类型暂时无法获取,值结果是%v \n", k, vv)
}
}
}

打印结果：
- map[Name:Wednesday Age:6 boolenValue:true Parents:[Gomez Morticia] Parents2:map[objectKey:Gomezdddd objectKey2:Morticiaaaaa]]
- boolenValue 是布尔型，值是：true
- Parents 是json Array类型，值是：[Gomez Morticia]
- Parents2 是json object类型，值是：map[objectKey:Gomezdddd objectKey2:Morticiaaaaa]
- Name 是字符型，值是：Wednesday
- Age 是数值型，值是：6
```