---
layout: post
title: Go语言 - YAML
date: 2020-08-13
tags: GoLang YAML
---

[**github** 地址]( [https://github.com/go-yaml/yaml](https://link.zhihu.com/?target=https%3A//github.com/go-yaml/yaml))

## `Marshal()`

`func Marshal(in interface{})(out []byte, err error)`

Marshal将提供的值序列化为YAML文档。生成的文档的结构将反映值本身的结构。map和指针(指向struct、string、int等)被接受为in值。

只有在导出(首字母为大写)时才对Struct字段进行编组，并使用字段名称小写作为默认键进行编组。可以通过字段标记中的“yaml”名称定义自定义键:第一个逗号前面的内容用作键，下面以逗号分隔的选项用于调整编组过程。名称冲突会导致运行时错误。

```
`(...) yaml:"[<key>][,<flag1>[,<flag2>]]" (...)`
```

```
omitempty    只有在未将该字段设置为类型的零值或空片或映射时才包含该字段。如果所有的公共字段都为零，值为零的struct将被省略，除非它们实现了IsZero方法(参见IsZeroer接口类型)，在这种情况下，如果IsZero返回true，则该字段将被排除。

flow         使用流样式(对struct、sequences和map很有用)进行封送。

inline       内联字段，它必须是结构体或映射，导致其所有字段或键被处理，就像它们是外部结构体的一部分一样。对于映射，键不能与其他结构字段的yaml键冲突。
```

```go
type T struct {
    F int `yaml:"a,omitempty"`
    B int
}
var t T
yaml.Unmarshal([]byte("a: 1\nb: 2"), &t)
```

