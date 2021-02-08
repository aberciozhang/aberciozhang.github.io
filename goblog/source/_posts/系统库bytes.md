---
title: 系统库bytes
tags:
  - null
  - null
categories: 系统库
copyright: true
top: 1
toc: true
sidebar: true
date: 2021-02-08 09:42:36
---
摘要:
    go语言短小精悍
    人生苦短,Let's Go!
    
<!-- more -->

### 库bytes的常量和变量

```
//MinRead是被Buffer.ReadFrom传递给Read调用的最小尺寸。只要该Buffer在保存内容之外有最少MinRead字节的余量，其ReadFrom方法就不会增加底层的缓冲。//
const MinRead = 512
//内存不能申请足够保存数据的缓冲，ErrTooLarge就被传递给panic函数
var ErrTooLarge = errors.New("bytes.Buffer: too large")
```

## 函数Runes

```
//byte转换城runes
func Runes(s []byte) []rune
```

### 函数Index系列

```
func Count(s, sep []byte) int
func Index(s, sep []byte) int
func IndexByte(s []byte, c byte) int
func IndexRune(s []byte, r rune) int
func IndexAny(s []byte, chars string) int
func IndexFunc(s []byte, f func(r rune) bool) int
func LastIndex(s, sep []byte) int
func LastIndexAny(s []byte, chars string) int
func LastIndexFunc(s []byte, f func(r rune) bool) int
```

### 函数Trim系列

```
func Trim(s []byte, cutset string) []byte
func TrimSpace(s []byte) []byte
func TrimFunc(s []byte, f func(r rune) bool) []byte
func TrimLeft(s []byte, cutset string) []byte
func TrimLeftFunc(s []byte, f func(r rune) bool) []byte
func TrimPrefix(s, prefix []byte) []byte
func TrimRight(s []byte, cutset string) []byte
func TrimRightFunc(s []byte, f func(r rune) bool) []byte
func TrimSuffix(s, suffix []byte) []byte
```

### 函数TO系列

```
func ToLower(s []byte) []byte
func ToLowerSpecial(_case unicode.SpecialCase, s []byte) []byte
func ToUpper(s []byte) []byte
func ToUpperSpecial(_case unicode.SpecialCase, s []byte) []byte
func ToTitle(s []byte) []byte
func ToTitleSpecial(_case unicode.SpecialCase, s []byte) []byte
```

### 函数Split系列

```
func Split(s, sep []byte) [][]byte
func SplitN(s, sep []byte, n int) [][]byte
func SplitAfter(s, sep []byte) [][]byte
func SplitAfterN(s, sep []byte, n int) [][]byte
```

### 常用函数

```
func Compare(a, b []byte) int
func Equal(a, b []byte) bool
func EqualFold(s, t []byte) bool
func HasPrefix(s, prefix []byte) bool
func HasSuffix(s, suffix []byte) bool
func Contains(b, subslice []byte) bool
func Title(s []byte) []byte
func Repeat(b []byte, count int) []byte
func Replace(s, old, new []byte, n int) []byte
func Map(mapping func(r rune) rune, s []byte) []byte
func Fields(s []byte) [][]byte
func FieldsFunc(s []byte, f func(rune) bool) [][]byte
func Join(s [][]byte, sep []byte) []byte
```

### 定义结构Reader

```
//Reader类型通过从一个[]byte读取数据，实现了io.Reader、io.Seeker、io.ReaderAt、io.WriterTo、io.ByteScanner、io.RuneScanner接口。//
type Reader struct {
    // 内含隐藏或非导出字段
}
```

### 结构Reader的方法

```
func NewReader(b []byte) *Reader
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

### 定义结构Buffer

```
//Buffer是一个实现了读写方法的可变大小的字节缓冲。本类型的零值是一个空的可用于读写的缓冲。//
type Buffer struct {
    // 内含隐藏或非导出字段
}
```

### 结构Buffer的方法

```
func NewBufferString(s string) *Buffer
func (b *Buffer) Reset()
func (b *Buffer) Len() int
func (b *Buffer) Bytes() []byte
func (b *Buffer) String() string
func (b *Buffer) Truncate(n int)
func (b *Buffer) Grow(n int)
func (b *Buffer) WriteTo(w io.Writer) (n int64, err error)
```

### 结构Buffer的方法Write系列

```
func (b *Buffer) Write(p []byte) (n int, err error)
func (b *Buffer) WriteString(s string) (n int, err error)
func (b *Buffer) WriteByte(c byte) error
func (b *Buffer) WriteRune(r rune) (n int, err error)
func NewBuffer(buf []byte) *Buffer
```

### 结构Buffer的方法Read系列

```
func (b *Buffer) Read(p []byte) (n int, err error)
func (b *Buffer) Next(n int) []byte
func (b *Buffer) ReadByte() (c byte, err error)
func (b *Buffer) UnreadByte() error
func (b *Buffer) ReadRune() (r rune, size int, err error)
func (b *Buffer) UnreadRune() error
func (b *Buffer) ReadBytes(delim byte) (line []byte, err error)
func (b *Buffer) ReadString(delim byte) (line string, err error)
func (b *Buffer) ReadFrom(r io.Reader) (n int64, err error)
```

