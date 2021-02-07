---
title: 系统库math
tags:
  - null
  - null
categories: 系统库
copyright: true
top: 1
toc: true
sidebar: true
date: 2021-02-07 14:26:56
---
摘要:
    go语言短小精悍
    人生苦短,Let's Go!
    
<!-- more -->

### 库math的常数和变量

```
//数学常数
const (
    E   = 2.71828182845904523536028747135266249775724709369995957496696763 // A001113
    Pi  = 3.14159265358979323846264338327950288419716939937510582097494459 // A000796
    Phi = 1.61803398874989484820458683436563811772030917980576286213544862 // A001622
    Sqrt2   = 1.41421356237309504880168872420969807856967187537694807317667974 // A002193
    SqrtE   = 1.64872127070012814684865078781416357165377610071014801157507931 // A019774
    SqrtPi  = 1.77245385090551602729816748334114518279754945612238712821380779 // A002161
    SqrtPhi = 1.27201964951406896425242246173749149171560804184009624861664038 // A139339
    Ln2    = 0.693147180559945309417232121458176568075500134360255254120680009 // A002162
    Log2E  = 1 / Ln2
    Ln10   = 2.30258509299404568401799145468436420760110148862877297603332790 // A002392
    Log10E = 1 / Ln10
)
```

```
//浮点数的取值极限
const (
    MaxFloat32             = 3.40282346638528859811704183484516925440e+38  // 2**127 * (2**24 - 1) / 2**23
    SmallestNonzeroFloat32 = 1.401298464324817070923729583289916131280e-45 // 1 / 2**(127 - 1 + 23)
    MaxFloat64             = 1.797693134862315708145274237317043567981e+308 // 2**1023 * (2**53 - 1) / 2**52
    SmallestNonzeroFloat64 = 4.940656458412465441765687928682213723651e-324 // 1 / 2**(1023 - 1 + 52)
)
```

```
//整数的取值极限
const (
    MaxInt8   = 1<<7 - 1
    MinInt8   = -1 << 7
    MaxInt16  = 1<<15 - 1
    MinInt16  = -1 << 15
    MaxInt32  = 1<<31 - 1
    MinInt32  = -1 << 31
    MaxInt64  = 1<<63 - 1
    MinInt64  = -1 << 63
    MaxUint8  = 1<<8 - 1
    MaxUint16 = 1<<16 - 1
    MaxUint32 = 1<<32 - 1
    MaxUint64 = 1<<64 - 1
)
```

### 常用函数

```
//函数返回一个IEEE 754“这不是一个数字”值。
func NaN() float64
//报告f是否表示一个NaN（Not A Number）值。
func IsNaN(f float64) (is bool)
//如果sign>=0函数返回正无穷大，否则返回负无穷大。
func Inf(sign int) float64
//如果sign > 0，f是正无穷大时返回真；如果sign<0，f是负无穷大时返回真；sign==0则f是两种无穷大时都返回真
func IsInf(f float64, sign int) bool
返回不小于x的最小整数（的浮点值）
func Ceil(x float64) float64
Ceil(±0) = ±0
Ceil(±Inf) = ±Inf
Ceil(NaN) = NaN

//返回不大于x的最大整数（的浮点值）
func Floor(x float64) float64
Floor(±0) = ±0
Floor(±Inf) = ±Inf
Floor(NaN) = NaN

//返回x的整数部分（的浮点值）
func Trunc(x float64) float64
Trunc(±0) = ±0
Trunc(±Inf) = ±Inf
Trunc(NaN) = NaN

//返回f的整数部分和小数部分，结果的正负号和都x相同
func Modf(f float64) (int float64, frac float64)
Modf(±Inf) = ±Inf, NaN
Modf(NaN) = NaN, NaN
//参数x到参数y的方向上，下一个可表示的数值；如果x==y将返回x
func Nextafter(x, y float64) (r float64)
Nextafter(NaN, y) = NaN
Nextafter(x, NaN) = NaN

func Abs(x float64) float64 //返回x的绝对值
func Max(x, y float64) float64//x和y中最大值
func Min(x, y float64) float64
//函数返回x-y和0中的最大值，
func Dim(x, y float64) float64
//取余运算，可以理解为 x-Trunc(x/y)*y，结果的正负号和x相同
func Mod(x, y float64) float64
func Sqrt(x float64) float64//返回x的二次方根
func Cbrt(x float64) float64//返回x的三次方根
func Hypot(p, q float64) float64//返回Sqrt(p*p + q*q)
func Sin(x float64) float64
func Cos(x float64) float64
func Tan(x float64) float64
func Sincos(x float64) (sin, cos float64)
func Asin(x float64) float64//求反正弦（x是弧度）
func Acos(x float64) float64//求反余弦（x是弧度）
func Atan(x float64) float64//求反正切（x是弧度
//类似Atan(y/x)，但会根据x，y的正负号确定象限
func Atan2(y, x float64) float64
func Log(x float64) float64
//等价于Log(1+x)。但是在x接近0时，本函数更加精确
func Log1p(x float64) float64
func Log2(x float64) float64//求2为底的对数
func Log10(x float64) float64
func Logb(x float64) float64//返回x的二进制指数值
func Pow(x, y float64) float64//返回x**y；
func Pow10(e int) float64//返回10**e
```

