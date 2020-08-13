---
layout: post
title: Go语言02 - 基础
date: 2020-08-12
tags: GoLang
---

# 一、基础内容

## 0. 流程控制

```go
for init; condition; post { }  // in C/C++: for(init; condition; post) { }
for condition { }			   // in C/C++: while(condition) { }
for { }						   // in C/C++: while(true) { }
```

### 循环语句 `range`

> 可以对 `slice` , `map` , `array` , `string` 进行迭代循环。

|             | 1st value | 2nd value |               |
| ----------- | --------- | --------- | ------------- |
| string      | index     | s[index]  | unicode, rune |
| array/slice | index     | s[index]  |               |
| map         | key       | m[key]    |               |
| channel     | element   |           |               |

`range` 中的 i, v 会从复制的对象中取出

```go
{% raw %}
// 若a为值类型, i, v 是从复制的a（原数据的拷贝）中获得，获得到的并不是原a的数据
a := [3]int{0, 1, 2}
for i, v := range a { // index、value 都是从复制品中取出。
    if i == 0 {
        a[1], a[2] = 777, 777
        fmt.Println(a) // [0, 777, 777]
    }
    a[i] = v + 100  // v != a[i]
}
fmt.Println(a)  // [100, 101, 102]
{% endraw %}
```

```go
{% raw %}
// 若a为引用类型, i, v 是从复制的a（仍为对原数据的引用）中获得，获得到的为原数据。
s := []int{1, 2, 3, 4, 5}
for i, v := range s {
	if i == 0 {
        s = s[:3]
        s[2] = 100
        fmt.Println(s)  // [1, 2, 100]
    }
    fmt.Println(v)
    // 1, 2, 100, 4, 5 复制的引用为[1:5]，复制后修改原引用为[:3]并不影响已复制的引用
}
{% endraw %}
```

#### `break` `continue` `goto`

```go
{% raw %}
for i := 1; i <= 3; i++ {
    fmt.Println(i)
    for j := 1; j <= 3; j++ {
        fmt.Print(j)
        break
        fmt.Print(j)
    }
}
/*
1
12
13
1
*/
label:
    for i := 1; i <= 3; i++ {
        fmt.Println(i)
        for j := 1; j <= 3; j++ {
            fmt.Print(j)
            break label
        	fmt.Print(j)
        }
    }
/*
1
1
*/
{% endraw %}
```

```go
{% raw %}
for i := 1; i <= 3; i++ {
    fmt.Println(i)
    for j := 1; j <= 3; j++ {
        fmt.Print(j)
        continue
        fmt.Print(j)
    }
}
/*
1
123
2
123
3
123
*/
label:
    for i := 1; i <= 3; i++ {
        fmt.Println(i)
        for j := 1; j <= 3; j++ {
            fmt.Print(j)
            continue label
        	fmt.Print(j)
        }
    }
/*
1
12
13
1
*/
{% endraw %}
```

```go
{% raw %}
i := 1
label:
	fmt.Print(i++)
	if i<10{
		goto label
	}
// 12345678910
{% endraw %}
```

## 1. 变量

### > 声明

使用关键字 `var` 声明变量。

```go
var identifier type
var identifier1, identifier2, type  // 类型相同多个变量，一般用于非全局变量
// "因式分解关键字"，一般用于全局变量
var (
	identifier3 type1
    identifier4 type2
)
```

```go
var year, month int = 2020, 8
var name string = "Azur-Ice"
// var year, month = 2020, 8  // 编译器根据值自动推断类型
// year, month := 2020, 8  // 简写（左侧必须是未声明的变量，且只能出现在函数体中）
```

> 声明的变量一定要被使用。

### > 赋值

```go
a = b  // b赋值给a
```

### > 匿名变量 `_`

> 不占用命名空间，不分配内存。

```go
{% raw %}
func foo() (int, string) {
    return 17, "Azur-Ice"
}
func main() {
    x, _ := foo()
    _, y := foo()
    fmt.Printf("x=%v, y=%v", x, y)
}
{% endraw %}
```

> 由于go语言中定义过的变量一定要使用，对于多返回值的函数，常常使用匿名变量来抛弃要忽略的返回值。

### 1> 数组

#### 声明

```go
var identifier [SIZE]type
```

```go
{% raw %}
var arr [5]float32{1000.0, 2.0, 3.4, 7.0, 50.0}
var arr0 [5]int = [5]int{1, 2, 3}				// 未初始化元素为默认值
var arr2 = [...]int{1, 2, 3, 4, 5, 6}			// 初推断类型、始化确定数组长度
var str = [5]string{3: "Azur", 4: "tom"}		// 索引号
{% endraw %}
```

#### 访问

`arr[index]`

#### 多维

```go
{% raw %}
var arr0 [5][3]int
var arr1 [2][3]int = [...][3]int{{1, 2, 3}, {7, 8, 9}}  // 只有第一维度可以使用 "..."
{% endraw %}
```

### 2> 切片

> 长度不固定，可追加元素 的 数组

#### 声明

```go
var identifier []type
```

```go
var slice1 []type = makr([]type, len)  // len 初始长度
slice2 := ([]T, length, capacity)  // capacity 容量（可选）
```

#### 切片初始化

```
s := []int{1,2,3}
```

直接初始化切片，[]表示是切片类型，{1,2,3}初始化值依次是1,2,3.其cap=len=3

```
s := arr[:] 
```

初始化切片s,是数组arr的引用

```
s := arr[startIndex:endIndex] 
```

将arr中从下标startIndex到endIndex-1 下的元素创建为一个新的切片

```
s := arr[startIndex:] 
```

默认 endIndex 为arr的最后一个元素下标

```
s := arr[:endIndex] 
```

默认 startIndex 为arr的第一个元素下标

```
s1 := s[startIndex:endIndex] 
```

通过切片s初始化切片s1

```
s :=make([]int,len,cap) 
```

> 切片是可索引的，并且可以由 len() 方法获取长度。
>
> 切片提供了计算容量的方法 cap() 可以测量切片最长可以达到多少。

#### `append()` 和 `copy()`

`append(slice, element[, element1, element2])` 将element（们）追加到slice末尾

`copy(slice1, slice)` 将slice中内容copy到slice1

### 3> map

> map是一种无序的基于key-value的数据结构，Go语言中的map是引用类型，必须初始化才能使用。

#### 定义

```go
map[KeyType]ValueType
```

#### 声明

```go
var m = make(map[KeyType]ValueType, [cap])
```

#### 1. 判断某个Key是否存在

```go
v, ok := v_map[key]
```

若存在，ok为true，v为对应的value。

若不存在，ok为false，v值类型的零值。

#### 2. 遍历



### 4> 结构体

#### 定义

```go
type struct_variable_type struct {
	member definition
	member definition
	...
	member definition
}
```

```go
type Books struct {
   title string
   author string
   subject string
   book_id int
}
```

#### 声明

```go
v_name := struct_variable_type {value1, value2, ..., valuen}
// v_name := struct_variable_type { key1: value1, key2: value2..., keyn: valuen}
```

```go
a := Books{"GoLang Basic", "Azur-Ice", "GoLang", 7777777}
b := Books{title: "2020Fighting", author: "Azur-Ice"}
```

#### 访问

`v.member`

### 5> 指针

`&` 和 `*` ......








## 2. 常量

关键字 `const`

> 同时声明多个常量时，如果省略了值则表示和上面一行的值相同。 

### > 特殊常量 iota

在 `const` 关键字出现时被重置为0，每新增一行常量声明将使 `iota` 自增1.

```go
const (
            a = iota   // a = 0, iota = 0
            b          // b = 1, iota = 1
            c          // c = 2, iota = 2
            d = "ha"   // d = "ha"，iota = 3
            e          // e = 4, iota = 4
            f = 100    // f = 100, iota =5
            g          // g = 6, iota =6
            h = iota   // g = 7, iota =7
            i          // g = 8, iota =8
    )
```

```
const (
	_ = iota	 // iota = 0
    i = 1<<iota  // i = 1<<1 = 2, iota=1
    j = 3<<iota  // j = 3<<2 = 12, iota=2
    k		     // k = 3<<3 = 24, iota=3
    l		     // l = 3<<4 = 48, iota=4
)
```

多个 `iota` 定义在一行

```go
const (
    a, b = iota + 1, iota + 2 //1,2
    c, d                      //2,3
    e, f                      //3,4
)
```

## 3. 函数

### > 定义

```go
func func_name( para1[, para2, ...] ) return_type1[, return_typr2, ...] {
	// do sth
    return v1[,v2, ...]
}
```

无返回值时不需要return

有返回值时必须return

### > 引用传递

定义 `*` ，传入 `&`



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
{% raw %}
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
{% endraw %}
```

### > 多维

```go
{% raw %}
//全局
var arr0 [5][3]int
var arr1 [2][3]int = [...][3]int{{1, 2, 3}, {7, 8, 9}}
//局部
a := [2][3]int{{1, 2, 3}, {4, 5, 6}}
b := [...][2]int{{1, 1}, {2, 2}, {3, 3}}  // 第 2 纬度不能用 "..."。
{% endraw %}
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

	