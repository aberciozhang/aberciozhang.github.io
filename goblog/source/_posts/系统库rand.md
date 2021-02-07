---
title: 系统库rand
tags:
  - null
  - null
categories: 系统库
copyright: true
top: 1
toc: true
sidebar: true
date: 2021-02-08 00:12:30
---
摘要:
    产生一个随机数
    Let's Go!
    
<!-- more -->

### 定义接口Source

```
//Source代表一个生成均匀分布在范围[0, 1<<63)的int64值的（伪随机的）资源
type Source interface {
    Int63() int64
    Seed(seed int64)
}
```

### 定义结构Rand

```
type Rand struct {
    // 内含隐藏或非导出字段
}
```

结构Rand的方法

```
func (r *Rand) Seed(seed int64)
func (r *Rand) Int() int
//返回一个int32类型的非负的31位伪随机数。
func (r *Rand) Int31() int32
func (r *Rand) Int63() int64
func (r *Rand) Uint32() uint32
//返回范围[0,n)伪随机int值，如果n<=0会panic
func (r *Rand) Intn(n int) int
func (r *Rand) Int31n(n int32) int32
func (r *Rand) Int63n(n int64) int64
func (r *Rand) Float32() float32
func (r *Rand) Float64() float64
func (r *Rand) NormFloat64() float64
func (r *Rand) ExpFloat64() float64
func (r *Rand) Perm(n int) []int
```

常用函数

```
func New(src Source) *Rand
func Seed(seed int64)
func Int() int
//返回一个int32类型的非负的31位伪随机数
func Int31() int32
func Int63() int64
func Uint32() uint32
func Intn(n int) int
func Int31n(n int32) int32
func Int63n(n int64) int64
func Float32() float32
func Float64() float64
func NormFloat64() float64
func ExpFloat64() float64
func Perm(n int) []int
```

定义结构Zipf

```
//Zipf生成服从齐普夫分布的随机数。
type Zipf struct {
    // 内含隐藏或非导出字段
}
```

函数NewZipf

```
func NewZipf(r *Rand, s float64, v float64, imax uint64) *Zipf
```

结构Zipf的Uint64方法

```
func (z *Zipf) Uint64() uint64
```

