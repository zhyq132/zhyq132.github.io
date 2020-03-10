---
title: "Go Module"
date: 2020-02-10T15:05:56+08:00
tags: ["golang"]
author: "七哥"
categories: ["Golang"]
---

## 对我的影响
> 在module出现之前，我用的一直就是GOPATH，其他dep、vender等方式没用过不做评价。对我来说使用GOPATH方式来管理包，最大的问题就是版本的依赖，在多个项目之前如果有版本的问题存在，简直就是灾难。

> 好在我完全专为go做开发后，go 1.13的stable版本已经发布，module已经非常成熟了，使用起来没有门槛，墙、私有包等都非常好用


## module优势
- 代理
> 使用go的同学，在入门阶段，最痛苦的记忆应该都是go get命令结果中的timeout，因为墙的原因，众多放在google的包，都无法下载，例如web开发中最常用的/x/net。这种限制，劝退了不少开发者。自从有了module，设置GOPROXY之后墙不再是问题。

- 自动发现依赖
> tidy子命令，自动发现项目中的依赖包

- 版本控制
> 众多依赖包的版本控制，尽在mod文件中掌握

- 私有库的搭建
> 大多数团队，拥有自己的私有库，go module对私有库的支持也很完善


## 设置方式
- 设置必要的环境变量
```
set GO111MODULE=on
set GOPROXY=https://goproxy.cn,direct
set GOPRIVATE=e.coding.net
set GONOPROXY=e.coding.net
set GONOSUMDB=e.coding.net
```
> 其中 GO111MODULE在go1.13是默认开启的。GOPROXY表示使用的代理，此后将优先从此host中获取第三方包。当使用自建库时，需要设置GOPRIVATE、GONOPROXY、GONOSUMDB三个环境变量，分表表示私有地址、不走代理地址、不去校验sum值的包来源

## 常用命令
> mod 我常用的命令就两个，如下，其他更多的以后用到会回来完善文档

- 初始化
```
go mod init [name]    在当前目录初始化项目名称
```

- 调整本目录下的包
```
go mod tidy [-v]    自动引入依赖包、去除无用的包，-v表示查看命令执行过程明细
```