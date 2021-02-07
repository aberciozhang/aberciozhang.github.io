---
title: 系统库strconv
tags:
  - null
  - null
categories: 系统库
copyright: true
top: 1
toc: true
sidebar: true
date: 2021-02-07 14:46:55
---
摘要:
    go语言短小精悍
    人生苦短,Let's Go!
    
<!-- more -->

### 库strconv常量和变量

```
const IntSize = intSize//IntSize是int或uint类型的字位数
//ErrRange表示超出目标类型表示范围。
var ErrRange = errors.New("value out of range")
//ErrSyntax表示不符合目标类型语法。
var ErrSyntax = errors.New("invalid syntax")
```

### 结构体NumError

```
//NumError表示一次失败的转换。
type NumError struct {
    Func string // 失败的函数（ParseBool、ParseInt、ParseUint、ParseFloat）
    Num  string // 输入的字符串
    Err  error  // 失败的原因（ErrRange、ErrSyntax）
}
//
func (e *NumError) Error() string
```

## 库strconv的常用函数

```
func IsPrint(r rune) bool
//返回字符串s是否可以不被修改的表示为一个单行的、没有空格和tab之外控制字符的反引号字符串
func CanBackquote(s string) bool
//返回字符串s在go语法下的双引号字面值表示，控制字符、不可打印字符会进行转义。如\t，\n
func Quote(s string) string
//返回字符串表示的bool值。它接受1、0、t、f、T、F、true、false、


//
func Atoi(s string) (i int, err error)
func Itoa(i int) string
```

### 函数Parse系列

```
//True、False、TRUE、FALSE；否则返回错误。
func ParseBool(str string) (value bool, err error)
//返回字符串表示的整数值，接受正负号
func ParseInt(s string, base int, bitSize int) (i int64, err error)
//类似ParseInt但不接受正负号，用于无符号整型。
func ParseUint(s string, base int, bitSize int) (n uint64, err error)
func ParseFloat(s string, bitSize int) (f float64, err error)
```

### 函数Format系列

```
//
func FormatBool(b bool) string
根据b的值返回"true"或"false"。
func FormatInt(i int64, base int) string
func FormatUint(i uint64, base int) string
func FormatFloat(f float64, fmt byte, prec, bitSize int) string
```

### 函数Append系列

```
//等价于append(dst, FormatBool(b)...)
func AppendBool(dst []byte, b bool) []byte
//等价于append(dst, FormatInt(I, base)...)
func AppendInt(dst []byte, i int64, base int) []byte
func AppendUint(dst []byte, i uint64, base int) []byte
func AppendFloat(dst []byte, f float64, fmt byte, prec int, bitSize int) []byte
func AppendQuote(dst []byte, s string) []byte
func AppendQuoteToASCII(dst []byte, s string) []byte
func AppendQuoteRune(dst []byte, r rune) []byte
//等价于append(dst, QuoteRuneToASCII(r)...)
func AppendQuoteRuneToASCII(dst []byte, r rune) []byte
```

