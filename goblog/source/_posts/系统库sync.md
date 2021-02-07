---
title: 系统库sync
tags:
  - mutex
  - sync
categories: 系统库
copyright: true
top: 1
toc: true
sidebar: true
date: 2021-02-08 01:43:01
---
摘要:
    go语言短小精悍
    人生苦短,Let's Go!
    
<!-- more -->

### 库sync常量和变量

```
var once Once
```

### 定义结构Locker

```
type Locker interface {
    Lock()
    Unlock()
}
```

### 定义结构Once及方法

```
//Once是只执行一次动作的对象
type Once struct {
    // 包含隐藏或非导出字段
}
func (o *Once) Do(f func())
```

### 定义结构Mutex及方法

```
//Mutex是一个互斥锁，可以创建为其他结构体的字段；零值为解锁状态。Mutex类型的锁和线程无关，可以由不同的线程加锁和解锁。
//
type Mutex struct {
    // 包含隐藏或非导出字段
}
func (m *Mutex) Lock()
func (m *Mutex) Unlock()
```

### 定义结构RWMutex

```
type RWMutex struct {
    // 包含隐藏或非导出字段
}
func (rw *RWMutex) Lock()
func (rw *RWMutex) Unlock()
func (rw *RWMutex) RLock()
func (rw *RWMutex) RUnlock()
func (rw *RWMutex) RLocker() Locker

//RWMutex是读写互斥锁。该锁可以被同时多个读取者持有或唯一个写入者持有。RWMutex可以创建为其他结构体的字段；零值为解锁状态。RWMutex类型的锁也和线程无关，可以由不同的线程加读取锁/写入和解读取锁/写入锁。
```

### 定义结构Cond

```
//Cond实现了一个条件变量，一个线程集合地，供线程等待或者宣布某事件的发生。
每个Cond实例都有一个相关的锁（一般是*Mutex或*RWMutex类型的值），它必须在改变条件时或者调用Wait方法时保持锁定。Cond可以创建为其他结构体的字段，Cond在开始使用后不能被拷贝。
type Cond struct {
    // 在观测或更改条件时L会冻结
    L Locker
    // 包含隐藏或非导出字段
}
func NewCond(l Locker) *Cond

//Broadcast唤醒所有等待c的线程。调用者在调用本方法时，建议（但并非必须）保持c.L的锁定。
func (c *Cond) Broadcast()

//Signal唤醒等待c的一个线程（如果存在）。调用者在调用本方法时，建议（但并非必须）保持c.L的锁定//
func (c *Cond) Signal()

//Wait自行解锁c.L并阻塞当前线程，在之后线程恢复执行时，Wait方法会在返回前锁定c.L。和其他系统不同，Wait除非被Broadcast或者Signal唤醒，不会主动返回。
func (c *Cond) Wait()


```

### 结构Cond的简例01

```
c.L.Lock()
for !condition() {
    c.Wait()
}
... make use of condition ...
c.L.Unlock()
```

### 定义结构WaitGroup

```
type WaitGroup struct {
    // 包含隐藏或非导出字段
}
func (wg *WaitGroup) Done()
func (wg *WaitGroup) Wait()
func (wg *WaitGroup) Add(delta int)
```

### 定义结构Pool

```
//Pool中保存的任何item都可能随时不做通告的释放掉。如果Pool持有该对象的唯一引用，这个item就可能被回收。
Pool可以安全的被多个线程同时使用。
Pool的目的是缓存申请但未使用的item用于之后的重用，以减轻GC的压力。也就是说，让创建高效而线程安全的空闲列表更容易。但Pool并不适用于所有空闲列表。
Pool的合理用法是用于管理一组静静的被多个独立并发线程共享并可能重用的临时item。Pool提供了让多个线程分摊内存申请消耗的方法。
Pool的一个好例子在fmt包里。该Pool维护一个动态大小的临时输出缓存仓库。该仓库会在过载（许多线程活跃的打印时）增大，在沉寂时缩小。
type Pool struct {
    // 可选参数New指定一个函数在Get方法可能返回nil时来生成一个值
    // 该参数不能在调用Get方法时被修改
    New func() interface{}
    // 包含隐藏或非导出字段
}
func (p *Pool) Get() interface{}
func (p *Pool) Put(x interface{})

```

定义结构

```
type Map struct {
	mu Mutex
	read atomic.Value // readOnly
	dirty map[interface{}]*entry
	misses int
}
func (m *Map) Load(key interface{}) (value interface{}, ok bool)
func (m *Map) Store(key, value interface{})
func (m *Map) LoadOrStore(key, value interface{}) (actual interface{}, loaded bool) 
func (m *Map) Delete(key interface{}) 
func (m *Map) Range(f func(key, value interface{}) bool)
```

