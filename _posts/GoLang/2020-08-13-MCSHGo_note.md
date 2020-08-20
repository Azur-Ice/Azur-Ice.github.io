---
layout: post
title: MCSHGo - 开发笔记
date: 2020-08-13
tags: GoLang MCSH Minecraft
---

# 交叉编译

```bash
set GOOS=linux
go build -o MCSH-version main.go

set GOOS=windows
go build -o MCSH-version.exe main.go
```

> set GOARCH=amd64

# v0.1-alpha

## 一、配置文件相关部分

### 1. 结构

- 目录结构

	```
	MCSHGO/
	│  config.yml
	│  MCSHGo
	│
	└─Scripts/
	        serverName1.bat
	        serverName2.bat
	        ...
	```

- 配置文件 `config.yml` ：

  ```Yaml
  servers:
  	serverName1:
  		rootFolder: path/to/your/server/root/folder
          scriptFile: script_file_name.xx
      serverName2:
  		rootFolder: path/to/your/server/root/folder
          scriptFile: script_file_name.xx
      ......
  ```

  >  所有服务器的 `scriptFile` 均存放于 `Scripts/` 中

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
		ScriptFile string `yaml:"scriptFile"`
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

### `os`

- `func IsNotExist(err error) bool`

	判断 `err` 是否表示一个文件或目录不存在（ErrNotExist和一些系统调用错误）。

- `func Exit(code int)`

	以状态码 `code` 退出程序

### [`gopkg.in/yaml.v3`](https://github.com/go-yaml/yaml)

- `func Marshal(in interface{}) (out []byte, err error)`

	> 输入 map/指针 转为 YAML []byte

	结构体中的字段标记：

	```go
	`(...) yaml:"[<key>][,<flag1>[,<flag2>]]" (...)`
	```

	flag：

	- **omitempty** 只有当非零值或空的slice/map是才包含该字段
	- **flow** 用 flow 样式进行 Marshal（对于结构体、序列、map很有用）
	- **inline** Inline the field, which must be a struct or a map,
		             causing all of its fields or keys to be processed as if
		             they were part of the outer struct. For maps, keys must
		             not conflict with the yaml keys of other struct fields.

- `func Unmarshal(in []byte, out interface{}) (err error)`

	> 输入 Yaml []byte，转为map/指针

### CODE

```go
{% raw %}
func readConfig() {
	configYaml, err := ioutil.ReadFile("./config.yml")
	if err != nil { // 读取文件发生错误
		if os.IsNotExist(err) { // 文件不存在，创建并写入默认配置
			log.Println("MCSH: Cannot find config.yml, creating...")
			ioutil.WriteFile("./config.yml", data2yaml(mcshConfig), 0666)
			log.Println("MCSH: Successful created config.yml, please complete the config.")
			os.Exit(1) //退出，提示完成config
		}
		fmt.Println(err) // 不是因为文件不存在而报错，输出并退出
		os.Exit(1)
	}
	mcshConfig = structMCSHConfig{}
	err = yaml.Unmarshal(configYaml, &mcshConfig) // 解析Yaml
}
{% endraw %}
```

## 3. 启动服务器

```go
{% raw %}
for name, serverConfig := range mcshConfig.Servers {
		go runServer(name, serverConfig, writeClosers)
		wg.Add(1)
	}
	go asyncForwardStdin()
	wg.Wait()
{% endraw %}
```

遍历 `mcshConfig.Servers` 中的每一个服务器，启动一个协程来 `runServer` ，将其 `stdin` 存入全局 `[]io.WriteCloser` 中，并在其中用两个协程分别启动对应 `stdout` 、 `stderr` 的转发。

随后在主线程中新开一协程来处理输入，`serverName|xxx` 将 `xxx` 写入对应服务器 `stdin`

```go
{% raw %}
func runServer(name string, serverConfig structServerConfig, writeClosers map[string]io.WriteCloser) error {
	if err := os.Chdir(serverConfig.RootFolder); err != nil {
		if os.IsNotExist(err) {
			log.Printf("server<%s>: Cannot find server root folder, please check your \"config.yml\"", name)
			wg.Done()
			return err
		}
		log.Printf("server<%s>: Error when chdir to server root folder - %s", name, err.Error())
		wg.Done()
		return err
	}
	cmd := exec.Command(path.Join(wd, "Scripts", serverConfig.ScriptFile))

	writeClosers[name], _ = cmd.StdinPipe()
	stdout, _ := cmd.StdoutPipe()
	stderr, _ := cmd.StderrPipe()

	if err := cmd.Start(); err != nil {
		log.Printf("server<%s>: Error when starting: %s......", name, err.Error())
		wg.Done()
		return err
	}
	go asyncLog(name, stdout)
	go asyncLog(name, stderr)

	if err := cmd.Wait(); err != nil {
		log.Printf("server<%s>: Error running: %s......", name, err.Error())
		wg.Done()
		return err
	}
	wg.Done()
	return nil
}
{% endraw %}
```

#### IO转发

```go
{% raw %}
func asyncLog(name string, readCloser io.ReadCloser) error {
	var outputReplaceRegString = `(\[\d\d:\d\d:\d\d\]) *\[.+?\/(.+?)\]`
	outputReplaceReg, err := regexp.Compile(outputReplaceRegString)
	if err != nil {
		log.Println("MCSH[outputForward/ERROR]: Regex compile failed - ", err)
	}
	cache := ""
	buf := make([]byte, 1024)
	for {
		num, err := readCloser.Read(buf)
		if err != nil && err != io.EOF {
			return err
		}
		if num > 0 {
			// b := buf[:num]
			s := outputReplaceReg.ReplaceAllString(string(buf[:num]), "["+name+"/$2]")
			lines := strings.Split(s, "\n")
			lines[0] = cache + lines[0]
			for i := 0; i < len(lines)-1; i++ {
				log.Println(lines[i])
			}
			cache = lines[len(lines)-1]
		}
	}
}
{% endraw %}
```

```go
{% raw %}
func asyncForwardStdin() {
	var forwardRegString = `(.+?) *\| *(.+)`
	forwardReg, errCompile := regexp.Compile(forwardRegString)
	if errCompile != nil {
		log.Println("MCSH[stdinForward/ERROR]: Regex compile failed - ", errCompile)
	}

	stdinReader := bufio.NewReader(os.Stdin)
	for {
		line, errRead := stdinReader.ReadBytes('\n')
		if errRead != nil {
			log.Println("MCSH[stdinForward/ERROR]: ", errRead)
		} else {
			line = line[:len(line)-1]
			if line[len(line)-1] == '\r' {
				line = line[:len(line)-1]
			}
			// log.Println(line)
			res := forwardReg.FindSubmatch(line)
			if res != nil {
				// log.Println(res)
				_, valid := writeClosers[string(res[1])]
				if valid {
					_, errWrite := writeClosers[string(res[1])].Write(append(res[2], '\n'))
					if errWrite != nil {
						log.Println("MCSH[stdinForward/ERROR]: Server stdin write failed - ", errWrite)
					}
				} else {
					log.Printf("MCSH[stdinForward/ERROR]: Cannot find running server <%v>\n", string(res[1]))
				}
			}
		}
	}
}
{% endraw %}
```

# v0.2-alpha

### `os/exec`

- `Command()`

	> 要完成文件后缀

	> linux下直接写脚本的文件名时，需要让脚本满足sh脚本语法
	>
	> 且需要chmod +x
	>
	> 否则需要 使用 `Command("bash", scriptFileName)` 才不会在 `cmd.start()` 时Panic