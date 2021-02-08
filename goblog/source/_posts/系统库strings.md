---
title: 系统库strings
tags:
  - strings
  - null
categories: 系统库
copyright: true
top: 1
toc: true
sidebar: true
date: 2021-02-07 14:47:10
---
摘要:
    go语言短小精悍
    人生苦短,Let's Go!
    
<!-- more -->

### 库strings常量和变量

### 函数Index

```
func Index(s, sep string) int
func IndexByte(s string, c byte) int
func IndexRune(s string, r rune) int
func IndexAny(s, chars string) int
func IndexFunc(s string, f func(rune) bool) int
func LastIndex(s, sep string) int
func LastIndexAny(s, chars string) int
func LastIndexFunc(s string, f func(rune) bool) int
```

### 函数To系列

```
func ToLower(s string) string
func ToLowerSpecial(_case unicode.SpecialCase, s string) string
func ToUpper(s string) string
func ToUpperSpecial(_case unicode.SpecialCase, s string) string
func ToTitle(s string) string
func ToTitleSpecial(_case unicode.SpecialCase, s string) string
```

### 函数Trim系列

```
func Trim(s string, cutset string) string
func TrimSpace(s string) string
func TrimFunc(s string, f func(rune) bool) string
func TrimLeft(s string, cutset string) string
func TrimLeftFunc(s string, f func(rune) bool) string
func TrimPrefix(s, prefix string) string
func TrimRight(s string, cutset string) string
func TrimRightFunc(s string, f func(rune) bool) string
func TrimSuffix(s, suffix string) string
```

### 函数Split系列

```
func Split(s, sep string) []string
func SplitN(s, sep string, n int) []string
func SplitAfter(s, sep string) []string
func SplitAfterN(s, sep string, n int) []string
```

函数Contains系列

```
//判断字符串s是否包含子串substr
func Contains(s, substr string) bool
//判断字符串s是否包含utf-8码值r。
func ContainsRune(s string, r rune) bool
//判断字符串s是否包含字符串chars中的任一字符。
func ContainsAny(s, chars string) bool
```

### 常用函数

```
//相等返0,a<b返-1,否则+1
func Compare(a, b string) int
//相等true,否则false
func EqualFold(s, t string) bool
//前缀有指定字符串返true,否则false
func HasPrefix(s, prefix string) bool
//后缀
func HasSuffix(s, suffix string) bool
返回字符串s中有几个不重复的sep子串
func Count(s, sep string) int
//首字母大写
func Title(s string) string
//返回count个s串联的字符串
func Repeat(s string, count int) string
//替换前n个
func Replace(s, old, new string, n int) string
//替换所有
func ReplaceAll(s, old, new string) string 
//将s分解成rune由指定函数操作,
func Map(mapping func(rune) rune, s string) string
//返回将字符串按照空白（unicode.IsSpace确定，可以是一到多个连续的空白字符）分割的多个字符串。如果字符串全部是空白或者是空字符串的话，会返回空切片。//
func Fields(s string) []string
//用函数f来确定分割符（满足f的unicode码值）。如果字符串全部是分隔符或者是空字符串的话，会返回空切片//
func FieldsFunc(s string, f func(rune) bool) []string
//一系列字符串连接为一个字符串，之间用sep来分隔
func Join(a []string, sep string) string
```

### 定义结构Reader

```
//Reader类型通过从一个字符串读取数据，实现了io.Reader、io.Seeker、io.ReaderAt、io.WriterTo、io.ByteScanner、io.RuneScanner接口。//
type Reader struct {
    // 内含隐藏或非导出字段
}
```

### 结构Reader的方法

```
func NewReader(s string) *Reader
func (r *Reader) Len() int
func (r *Reader) Read(b []byte) (n int, err error)
func (r *Reader) ReadByte() (b byte, err error)
func (r *Reader) UnreadByte() error
func (r *Reader) ReadRune() (ch rune, size int, err error)
func (r *Reader) UnreadRune() error
func (r *Reader) Seek(offset int64, whence int) (int64, error)
func (r *Reader) ReadAt(b []byte, off int64) (n int, err error)
func (r *Reader) WriteTo(w io.Writer) (n int64, err error)
```

### 定义结构Replacer

```
//Replacer类型进行一系列字符串的替换
type Replacer struct {
    // 内含隐藏或非导出字段
}
```

### 结构Replacer的方法

```
func NewReplacer(oldnew ...string) *Replacer
func (r *Replacer) Replace(s string) string
func (r *Replacer) WriteString(w io.Writer, s string) (n int, err error)
```

### 常用函数的简例01

```
package main
func main() {
	s := string([]byte("abc"))//byte转换成string
	b:=string([]rune{'a','b','d'})//rune转换成string
	fmt.Println(strings.EqualFold(s,"abc"))   //true
	fmt.Println(strings.Compare(s,b))     //-1
	fmt.Println(strings.HasPrefix(s,"a")) //true
	fmt.Println(strings.HasSuffix(s,"a")) //false
	fmt.Println(strings.Contains(s,"b"))  //true
	fmt.Println(strings.Count(s,"ab"))//1
	fmt.Println(strings.Title(s))//Abc
	fmt.Println(strings.Repeat(s,2))//abcabc
	fmt.Println(strings.Replace("1b1b1b","b","x",2))//1x1x1b
fmt.Println(strings.ReplaceAll("1b1b1b","b","x"))//1x1x1x
	m:=strings.Map(mapstring,"ab汉字")
	fmt.Println(m)//yyyy
	//空白符作为分隔符
	ary:= strings.Fields("abc def hij")
	fmt.Printf("%v\n",ary)//[abc def hij]
	fs:=strings.FieldsFunc("abc&edf^12f", fieldsdo)
	fmt.Println(fs)//[abc edf 12f]
	arys2 := []string{"abc","xyz"}//
	fmt.Println(strings.Join(arys2,"$"))//abc$xyz
}
func fieldsdo(r rune) bool {
	if r==rune('&')||r==rune('^') {return true}
	return false
}
func mapstring(r rune) rune {
	return rune('y')
}
```

