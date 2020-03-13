---
title: "golang-初次接触gotrace"
date: 2017-11-21T20:04:58+08:00
tags: ["golang"]
author: "七哥"
categories: ["Golang"]
---

> 我本地环境是vagrant、docker混合的。vagrant主要负责同步wins与vagrant-box之间的文件，在vagrant启动后的虚拟机里，安装有docker环境，分别跑有nginx、php5、php7. gotrace需要在运行时打开浏览器，所以vagrant-box（ubuntu1604-server版）无法做到，所以gotrace要先生成exe文件

## 安装
```
1. go get -u github.com/divan/gotrace，使用go安装gotrace，win宿主机、vagrant-box都要装一遍
2. 在对应的go代码中加入trace.Start(os.Stderr)、trace.Stop()代码
3. sudo docker run --rm -it -e GOOS=windows -v /home/ubuntu/mygo/src:/src divan/golang:gotrace go build -o /src/binary.exe /src/zhyq132/cst/a.go；GOOS这个环境变量要显示声明为win环境，binary也要添加exe后缀
4. ./binary.exe 2 trace.out
5. gotrace.exe ./trace.out
```

## 注意上述过程中有两个坑
1.第一个是go get 获取的gotrace包，要切换为go18版本后执行go install编译执行后获取gotrace.exe文件，以便于步骤5中使用.作者divan在gotrace项目中的issue里回答过这个bug
2.现在是2017-11-21日，官方文档里最后一步也就是上述的步骤5还是gotrace.exe ./trace.out ./binary.exe，实际执行过程中，上述切换为go18后，最后一步实际应该为：gotrace.exe ./trace.out