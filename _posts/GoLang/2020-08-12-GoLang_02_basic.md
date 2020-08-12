---
layout: post
title: Go语言02 - 基础
date: 2020-08-12
tags: GoLang
---

# 一、注释 （C/C++）

- 单行注释

	```go
	// xxxx
	```

- 多行注释

	```go
	/*
		xxxxx
		xxxxxxx
		xx
		xxxx
	*/
	```

# 运算符（C/C++）



# 二、声明

> 命名：
>
> 1. 首字符可以是任意的Unicode字符或下划线
> 2. 剩余字符可以是Unicode字符、下划线、数字，长度不限

- `var` 变量
- `const` 常量
- `type` 类型
- `func` 函数

# 三、类型

## 1. 值类型

`bool`

`int(32 or 64), int8, int16, int32, int64`

`uint(32 or 64), uint8, uint16, uint32, uint64`

`float32, float64`

`string`

`complex64, complex128`

`array` 定长数组

## 2. 引用（指针）类型

`slice` 序列数组（常用

`map` 映射

`chan` 管道

# 四、内置函数

`append` 追加元素到数组、slice中，返回修改后的数组、slice。

`close` 关闭channel

`delete` 从map中删除key对应的value

`panic` 停止常规的goroutine（错误处理）

`recover` 允许程序定义goroutine的panic动作（错误处理）

`imag` 返回complex的实部（complex、real imag用于创建、操作复数）

`real` 返回complex的虚部

`make` 分配内存，返回Type本身（只能用于slice、map、channel）

`new` 分配内存，主要用来分配值类型（int、struct）返回指向Type的指针

`cap` 返回某类型的最大容量（只能用于slice和map）

`copy` 复制、连接slice，返回复制的数目

`len` 返回长度（string、array、slice、map、channel）

`print、println` 底层打印函数，建议使用fmt包

# 五、Init函数和main函数

init可以应用于任意包中，在main函数执行之前自动被调用，按照依赖关系据欸的那个执行顺序，每个包及其每个源文件可有多个init函数。

main函数只能用于main包中，唯一，程序入口。

# 六、特殊标识符 _

用来忽略结果

import 中仅调用init函数

代码中表示忽略变量

# 七、 变量

## 1. 变量声明与初始化

### > 标准声明与初始化

```go
var 变量名 变量类型
// var 变量名 类型 = 表达式
```

> 例：
>
> ```go
> var name string
> var age
> // var name string = "Azur-Ice"
> // var age int = "17"
> ```

### > 批量声明与初始化

```go
var year, mounth, day int
// var year, month, day int = 2020, 8, 12
```

```go
// "因式分解关键字", 一般用于声明全局变量
var (
	year, month int
	name        string
)
/*
var (
	year, month int    = 2020, 8
	name        string = "Azur-Ice"
)
*/
```

### > 类型推导

有时候可以忽略变量的类型，编译器会自动根据等号右侧的值来推导变量类型。

```go
var name, age = "Azur-Ice", 17
```

### > 简化

```go
// 一般只能在函数体中出现，左侧必须是未声明过的变量
name, age := "Azur-Ice", 17
```

> go语言 变量定义后必须要使用

## 2, 基本类型

### > 整型
| 类型          | 长度(字节) | 默认值 | 说明                                      |
| ------------- | ---------- | ------ | ----------------------------------------- |
| int, uint     | 4或8       | 0      | 32 或 64 位                               |
| int8, uint8   | 1          | 0      | -128 ~ 127, 0 ~ 255，byte是uint8 的别名   |
| int16, uint16 | 2          | 0      | -32768 ~ 32767, 0 ~ 65535                 |
| int32, uint32 | 4          | 0      | -21亿~ 21亿, 0 ~ 42亿，rune是int32 的别名 |
| int64, uint64 | 8          | 0      |                                           |
### > 浮点型
| 类型          | 长度(字节) | 默认值 | 说明                                      |
| ------------- | ---------- | ------ | ----------------------------------------- |
| float32       | 4          | 0.0    |                                           |
| float64       | 8          | 0.0    |                                           |
### > 复数
| 类型          | 长度(字节) | 默认值 | 说明                                      |
| ------------- | ---------- | ------ | ----------------------------------------- |
| complex64     | 8          |        |                                           |
| complex128    | 16         |        |                                           |
### > 布尔值
| 类型          | 长度(字节) | 默认值 | 说明                                      |
| ------------- | ---------- | ------ | ----------------------------------------- |
| bool          | 1          | false  |                                           |
### > 字符串
| 类型          | 长度(字节) | 默认值 | 说明                                      |
| ------------- | ---------- | ------ | ----------------------------------------- |
| string        |            | ""     | UTF-8 字符串                              |
方法	介绍
len(str)	求长度
+或fmt.Sprintf	拼接字符串
strings.Split	分割
strings.Contains	判断是否包含
strings.HasPrefix,strings.HasSuffix	前缀/后缀判断
strings.Index(),strings.LastIndex()	子串出现的位置
strings.Join(a[]string, sep string)	join操作
### > byte和rune（字符）
| 类型          | 长度(字节) | 默认值 | 说明                                      |
| ------------- | ---------- | ------ | ----------------------------------------- |
| byte          | 1          | 0      | uint8的别名 ASCII                               |
| rune          | 4          | 0      | int32 UTF-8                |
### > 其他
| 类型          | 长度(字节) | 默认值 | 说明                                      |
| ------------- | ---------- | ------ | ----------------------------------------- |
| uintptr       | 4或8       |        | 以存储指针的 uint32 或 uint64 整数        |
| array         |            |        | 值类型                                    |
| struct        |            |        | 值类型                                    |
| slice         |            | nil    | 引用类型                                  |
| map           |            | nil    | 引用类型                                  |
| channel       |            | nil    | 引用类型                                  |
| interface     |            | nil    | 接口                                      |
| function      |            | nil    | 函数                                      |

> 支持八进制、 六进制，以及科学记数法。标准库 math 定义了各数字类型取值范围。
>
> ```go
>  a, b, c, d := 071, 0x1F, 1e9, math.MinInt16
> ```

> 空指针值未nil，而非NULL

# 八、数组

## 1. 初始化

### > 一维数组

```go
var arr0 [5]int=[5]int{1, 2, 3}  // 未初始化元素为默认值
var arr1 = [5]int{1, 2, 3, 4, 5}
var arr2 = [...]int{1, 2, 3, 4, 5, 6}  // 初始化确定数组长度
var str = [5]string{3: "Azur", 4: "tom"}  // 索引号
// 结构体数组
d := [...]struct {
    name string
    age  uint8
}{
    {"user1", 10}, // 可省略元素类型。
    {"user2", 20}, // 别忘了最后一行的逗号。
}
```

### > 多维

```go
//全局
var arr0 [5][3]int
var arr1 [2][3]int = [...][3]int{{1, 2, 3}, {7, 8, 9}}
//局部
a := [2][3]int{{1, 2, 3}, {4, 5, 6}}
b := [...][2]int{{1, 1}, {2, 2}, {3, 3}}  // 第 2 纬度不能用 "..."。
```



## 2. 



# fmt包

## 输出

- `Print("A", "B", a)` 不换行

	```
	ABaaa
	```

- `Println("A", "B", a)` 换行

	```
	A
	B
	aaa
	```

- `Printf("%v", a)` 不换行，格式化输出

	```
	aaa
	```

	