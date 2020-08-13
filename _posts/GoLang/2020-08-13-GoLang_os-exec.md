---
layout: post
title: Go语言 - os/exec包
date: 2020-08-13
tags: GoLang
---

# `exec` 包

## 1. `Command()`

`func Command(name string, arg ...string)*Cmd`



```go
type Cmd struct {
	Path         string　　　//运行命令的路径，绝对路径或者相对路径
	Args         []string　　 // 命令参数
	Env          []string         //进程环境，如果环境为空，则使用当前进程的环境
	Dir          string　　　//指定command的工作目录，如果dir为空，则comman在调用进程所在当前目录中运行
	Stdin        io.Reader　　//标准输入，如果stdin是nil的话，进程从null device中读取（os.DevNull），stdin也可以时一个文件，否则的话则在运行过程中再开一个goroutine去
　　　　　　　　　　　　　//读取标准输入
	Stdout       io.Writer       //标准输出
	Stderr       io.Writer　　//错误输出，如果这两个（Stdout和Stderr）为空的话，则command运行时将响应的文件描述符连接到os.DevNull
	ExtraFiles   []*os.File 　　
	SysProcAttr  *syscall.SysProcAttr
	Process      *os.Process    //Process是底层进程，只启动一次
	ProcessState *os.ProcessState　　//ProcessState包含一个退出进程的信息，当进程调用Wait或者Run时便会产生该信息．
}
```

## 1. `LookPath()`

`Func LookPath(file string) (string, error)`

在环境变量中查找科执行二进制文件，如果file中包含一个斜杠，则直接根据绝对路径或者相对本目录的相对路径去查找