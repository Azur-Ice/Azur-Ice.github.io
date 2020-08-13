---
layout: post
title: MCSHGo - GoLang笔记
date: 2020-08-13
tags: GoLang MCSH Minecraft
---

## 一、配置文件相关部分

### 1. 结构

- 配置文件 `config.yml` ：

	```Yaml
	servers:
		serverName1:
			rootFolder: path/to/your/server/root/folder
	        scriptPath: path/to/your/script/file
	    serverName2:
			rootFolder: path/to/your/server/root/folder
	        scriptPath: path/to/your/script/file
	    ......
	```

- 结构体

	```go
	{% raw %}
	// 对应config.yml
	type structMCSHConfig struct {
		Servers map[string]structServerConfig `yaml:"servers"`
	}
{% endraw %}
	```
	
	```go
	{% raw %}
	// 对应servers中的元素
	type structServerConfig struct {
		RootFolder string `yaml:"rootFolder"`
		ScriptPath string `yaml:"scriptPath"`
	}
	{% endraw %}
	```

## 2. 配置文件读取

### `io/ioutil`

- `ReadFile(filename string) ([]byte, error)`

	从 **filename** 指定文件中读取数据并以 `[]byte` 类型返回内容。

	若成功调用，返回的 **err** 为 **nil**

- `WriteFile(filename string, data []byte, perm os.FileMode) error`

	将 **filename** 指定文件清空，并向其中写入 **data** 。

	若文件不存在，则按照 **perm** 给出的权限创建文件。

	> linux文件权限系统中有三种可以拥有的访问权限：
	>
	> **r** 可读， **w** 可写， **x** 可执行
	>
	> 分别对应 4， 2， 1，分别为 1<<2, 1<<1, 1<<0 三个二进制位
	>
	> 每个文件有4个权限位： 特殊权限位，拥有者位，同组用户位，其余用户位
	>
	> 所以每一个文件的权限可以用4个八进制数来表示，一般特殊权限位为0。
	
	> 例：拥有者、同组用户、其余用户 可读可写： (0)(2+4)(2+4)(2+4) = 0666





管道

```go
ch := make(chan int, size)
ch <- 22 //存储进管道
ch <- 33
ch <- 77

// 获取管道内容
data1 := <- ch // 22
data2 := <-ch  // 33
data2 := <- ch // 77
```



