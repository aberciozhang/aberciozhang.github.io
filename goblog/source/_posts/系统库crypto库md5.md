---
title: 系统库crypto库md5
tags:
  - crypto
  - md5
categories: 系统库
copyright: true
top: 1
toc: true
sidebar: true
date: 2021-02-08 02:31:26
---
摘要:
    go语言短小精悍
    人生苦短,Let's Go!
    
<!-- more -->

### 库md5的常量和变量

```
const BlockSize = 64//MD5字节块大小
const Size = 16//MD5校验和字节数
```

### 函数Sum

```
//数据data的MD5校验和
func Sum(data []byte) [Size]byte
```

### 函数New

```
//新的使用MD5校验的hash.Hash接口
func New() hash.Hash
```

库md5的简例01

```
package main
func main() {
	s := Md5Demo("fullstack!fullbook")
	fmt.Printf("%x\n", s)
	fmt.Println()
	Md5HashDemo("fullstack!", "fullbook")	
}
func Md5Demo(s string) [16]byte {
	data := []byte(s)
	return md5.Sum(data)
}
//返回一个新的使用MD5校验的hash.Hash接口。
func Md5HashDemo(s,s1 string){
	h := md5.New()
	io.WriteString(h, s)
	io.WriteString(h, s1)
	fmt.Printf("%x\n", h.Sum(nil))
}
```

库md5的简例02

```
package main
func main() {
	str := "abc123"
	//方法一
	data := []byte(str)
	has := md5.Sum(data)
	md5str1 := fmt.Sprintf("%x", has) //将[]byte转成16进制
	fmt.Println(md5str1)
	//方法二
	w := md5.New()
	io.WriteString(w, str)   //将str写入到w中
	//w.Sum(nil)将w的hash转成[]byte格式
	md5str2 := fmt.Sprintf("%x", w.Sum(nil))
	fmt.Println(md5str2)
	//
	h := md5.New()
	h.Write(data)
	s := hex.EncodeToString(h.Sum(nil))
	fmt.Println(s)
}

```