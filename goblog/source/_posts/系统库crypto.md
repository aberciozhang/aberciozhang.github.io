---
title: 系统库crypto
tags:
  - crypto
  - hash
categories: 系统库
copyright: true
top: 1
toc: true
sidebar: true
date: 2021-02-08 02:20:43
---
摘要:
    go语言短小精悍
    人生苦短,Let's Go!
    
<!-- more -->

## 库crypto的常量和变量

```
const (
    MD4       Hash = 1 + iota // 导入code.google.com/p/go.crypto/md4
    MD5                       // 导入crypto/md5
    SHA1                      // 导入crypto/sha1
    SHA224                    // 导入crypto/sha256
    SHA256                    // 导入crypto/sha256
    SHA384                    // 导入crypto/sha512
    SHA512                    // 导入crypto/sha512
    MD5SHA1                   // 未实现；MD5+SHA1用于TLS RSA
    RIPEMD160                 // 导入code.google.com/p/go.crypto/ripemd160
)
```

## 定义接口PublicKey

```
//代表一个使用未指定算法的公钥。
type PublicKey interface{}
```

## 定义接口PrivateKey

```
//代表一个使用未指定算法的私钥。
type PrivateKey interface{}
```

## 定义类型Hash

```
//Hash用来识别/标识另一个包里实现的加密函数
type Hash uint
func (h Hash) Available() bool
func (h Hash) Size() int
func (h Hash) New() hash.Hash

```

### 函数RegisterHash

```
func RegisterHash(h Hash, f func() hash.Hash)
```