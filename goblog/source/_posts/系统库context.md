---
title: 系统库context
tags:
  - context
  - null
categories: 系统库
copyright: true
top: 1
toc: true
sidebar: true
date: 2021-02-07 23:26:49
---
摘要:
    go语言短小精悍
    人生苦短,Let's Go!
    
<!-- more -->

### 库context常量和变量

```
var Canceled = errors.New("context canceled")
var DeadlineExceeded error = deadlineExceededError{}
```

### 定义函数CancelFunc

```
type CancelFunc func()
```

### 定义接口Context

```
type Context interface {    
    Deadline() (deadline time.Time, ok bool)
    // a Done channel for cancelation.
    Done() <-chan struct{}
    Err() error
    Value(key interface{}) interface{}
}
```

### 常用函数

```
func Background() Context
func TODO() Context
```

### 函数With系列

```
func WithCancel(parent Context) (ctx Context, cancel CancelFunc)
func WithDeadline(parent Context, deadline time.Time) (Context, CancelFunc)
func WithTimeout(parent Context, timeout time.Duration) (Context, CancelFunc)

func WithValue(parent Context, key, val interface{}) Context

```

### 函数WithTimeout简例01

```
func slowOperationWithTimeout(ctx context.Context) (Result, error) {
	ctx, cancel := context.WithTimeout(ctx, 100*time.Millisecond)
	defer cancel()  
	return slowOperation(ctx)
}
```

### 函数WithValue简例02

```
package main
func main() {
	type favContextKey string

	f := func(ctx context.Context, k favContextKey) {
		if v := ctx.Value(k); v != nil {
			fmt.Println("found value:", v)
			return
		}
		fmt.Println("key not found:", k)
	}

	k := favContextKey("language")
	ctx := context.WithValue(context.Background(), k, "Go")
	f(ctx, k)
	f(ctx, favContextKey("color"))
}
```

