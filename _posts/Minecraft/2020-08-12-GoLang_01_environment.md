---
layout: post
title: Go语言01 - 环境配置
date: 2020-08-12
tags: GoLang
---



# 一、安装 GoLang

## 1. 下载

- [官网 https://golang.org/dl/](https://golang.org/dl/)

- [官方镜像站 https://golang.google.cn/dl/](https://golang.google.cn/dl/)

## 2. 安装

下一步

## 3. 验证

使用 `go version` 查看版本，验证是否安装成功。

```
C:\Users\xiaob>go version
go version go1.15 windows/amd64
```

使用 `go env` 查看go语言环境变量。

```
C:\Users\xiaob>go env
set GO111MODULE=on
set GOARCH=amd64
set GOBIN=
set GOCACHE=C:\Users\xiaob\AppData\Local\go-build
set GOENV=C:\Users\xiaob\AppData\Roaming\go\env
set GOEXE=.exe
set GOFLAGS=
set GOHOSTARCH=amd64
set GOHOSTOS=windows
set GOINSECURE=
set GOMODCACHE=C:\Users\xiaob\go\pkg\mod
set GONOPROXY=
set GONOSUMDB=
set GOOS=windows
set GOPATH=C:\Users\xiaob\go
set GOPRIVATE=
set GOPROXY=https://goproxy.cn,https://gocenter.io,https://goproxy.io,direct
set GOROOT=D:\_Dev\Go
set GOSUMDB=sum.golang.org
set GOTMPDIR=
set GOTOOLDIR=D:\_Dev\Go\pkg\tool\windows_amd64
set GCCGO=gccgo
set AR=ar
set CC=gcc
set CXX=g++
set CGO_ENABLED=1
set GOMOD=NUL
set CGO_CFLAGS=-g -O2
set CGO_CPPFLAGS=
set CGO_CXXFLAGS=-g -O2
set CGO_FFLAGS=-g -O2
set CGO_LDFLAGS=-g -O2
set PKG_CONFIG=pkg-config
set GOGCCFLAGS=-m64 -mthreads -fno-caret-diagnostics -Qunused-arguments -fmessage-length=0 -fdebug-prefix-map=C:\Users\xiaob\AppData\Local\Temp\go-build908148402=/tmp/go-build -gno-record-gcc-switches
```

> go 1.11 之后已无需手动配置 GOPATH 等环境变量，可使用 go mod `GO111MODULE=on` 管理包。

> go 1.13 之后已可彻底不需要使用 GOPATH

# 二、安装 IDE/编辑器

## 1. VSCode

安装插件 **go**

新建项目文件夹

打开文件夹

新建go语言文件

例：

```
H:\PROJECTS - GO\MCSHGO
    main.go
```



```go
package main

import "fmt"

func main(){
	fmt.Println("HelloWorld!")
}
```

调试：

运行 `go run main.go`



