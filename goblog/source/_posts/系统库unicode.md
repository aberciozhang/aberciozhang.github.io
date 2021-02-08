---
title: 系统库unicode
tags:
  - unicode
  - rune
categories: 系统库
copyright: true
top: 1
toc: true
sidebar: true
date: 2021-02-08 11:54:43
---
摘要:
    go语言短小精悍
    人生苦短,Let's Go!
    
<!-- more -->

### 库unicode常量和变量

```
const (
    MaxRune         = '\U0010FFFF' // 最大的合法unicode码值
    ReplacementChar = '\uFFFD'     // 表示不合法的unicode码值
    MaxASCII        = '\u007F'     // 最大的ASCII值
    MaxLatin1       = '\u00FF'     // 最大的Latin-1值
)
const (
    UpperCase = iota
    LowerCase
    TitleCase
    MaxCase
)
const (
    UpperLower = MaxRune + 1 // 不能是合法的delta值
)
const Version = "6.3.0"

```

### 函数Is系列

```
func Is(rangeTab *RangeTable, r rune) bool
func IsOneOf(ranges []*RangeTable, r rune) bool
func IsSpace(r rune) bool
func IsDigit(r rune) bool
func IsNumber(r rune) bool
func IsLetter(r rune) bool
func IsGraphic(r rune) bool
func IsControl(r rune) bool
func IsMark(r rune) bool
func IsPrint(r rune) bool
func IsPunct(r rune) bool
func IsSymbol(r rune) bool
func IsLower(r rune) bool
func IsUpper(r rune) bool
//返回字符是否是标题字母
func IsTitle(r rune) bool
```

### 函数To系列

```
func To(_case int, r rune) rune

func ToLower(r rune) rune
func ToUpper(r rune) rune
//返回对应的标题字母
func ToTitle(r rune) rune
```

### 常用函数

```
func In(r rune, ranges ...*RangeTable) bool
func SimpleFold(r rune) rune
```

### 定义结构RangeTable

```
type RangeTable struct {
    R16         []Range16
    R32         []Range32  // R32不能包含低于0x10000（即1<<16）的值
    LatinOffset int // R16字段中Hi <= MaxLatin1的成员数
}
```

### 定义Range16

```
type Range16 struct {
    Lo     uint16
    Hi     uint16
    Stride uint16
}
```

### 定义Range32

```
type Range32 struct {
    Lo     uint32
    Hi     uint32
    Stride uint32
}
```

### 定义CaseRange

```
代表简单的unicode码值的一一映射。范围为[Lo, Hi]，步长为1。
该范围内的每个值+Delta[UpperCase]表示对应的大写字母；
该范围内的每个值+Delta[LowerCase]表示对应的小写字母；
该范围内的每个值+Delta[TitleCase]表示对应的标题字母。
Delta数组里的值可为负数或零。如果Delta数组是：
```

```
type CaseRange struct {
    Lo    uint32
    Hi    uint32
    Delta d // d为[MaxCase]rune的命名类型
}
```

### 定义类型SpecialCase

```
//SpecialCase代表特定语言的字符映射，如土耳其语。本类型的方法（通过覆盖）定制了标准映射//
type SpecialCase []CaseRange
//栗子
var AzeriCase SpecialCase = _TurkishCase
var TurkishCase SpecialCase = _TurkishCase
```

### 类型SpecialCase的方法

```
func (special SpecialCase) ToLower(r rune) rune
func (special SpecialCase) ToUpper(r rune) rune
func (special SpecialCase) ToTitle(r rune) rune
```

### 函数Is系列简例01

```
package main
func main() {
	s := "wo阿斯蒂ni海上"
	r1 := []rune(s[:])
	//r2 := []rune{'人','我','c'}
	for _, v := range r1 {
		if unicode.Is(unicode.Han, v) {
			fmt.Printf("%c\n", v)
		}
	}
	fmt.Println(rune('9')) //unicode编码57
	fmt.Println(unicode.IsDigit(57))//true,9,
	fmt.Println(unicode.IsLower(97), rune('a')) //true 97
}
```

