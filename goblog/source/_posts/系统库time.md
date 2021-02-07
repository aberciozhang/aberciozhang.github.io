---
title: 系统库time
tags:
  - time
  - null
categories: 系统库
copyright: true
top: 1
toc: true
sidebar: true
date: 2021-02-07 15:00:17
---
摘要:
    go语言短小精悍
    人生苦短,Let's Go!
    
<!-- more -->

### 库time常量和变量

```
const (
    ANSIC       = "Mon Jan _2 15:04:05 2006"
    UnixDate    = "Mon Jan _2 15:04:05 MST 2006"
    RubyDate    = "Mon Jan 02 15:04:05 -0700 2006"
    RFC822      = "02 Jan 06 15:04 MST"
    RFC822Z     = "02 Jan 06 15:04 -0700" // 使用数字表示时区的RFC822
    RFC850      = "Monday, 02-Jan-06 15:04:05 MST"
    RFC1123     = "Mon, 02 Jan 2006 15:04:05 MST"
    RFC1123Z    = "Mon, 02 Jan 2006 15:04:05 -0700" // 使用数字表示时区的RFC1123
    RFC3339     = "2006-01-02T15:04:05Z07:00"
    RFC3339Nano = "2006-01-02T15:04:05.999999999Z07:00"
    Kitchen     = "3:04PM"
    // 方便的时间戳
    Stamp      = "Jan _2 15:04:05"
    StampMilli = "Jan _2 15:04:05.000"
    StampMicro = "Jan _2 15:04:05.000000"
    StampNano  = "Jan _2 15:04:05.000000000"
)
```

### 结构ParseError

```
type ParseError struct {
    Layout     string
    Value      string
    LayoutElem string
    ValueElem  string
    Message    string
}
```

### 类型Weekday

```
type Weekday int
const (
    Sunday Weekday = iota
    Monday
    Tuesday
    Wednesday
    Thursday
    Friday
    Saturday
)
//String返回该日（周几）的英文名如"Sunday"
func (d Weekday) String() string
```

### 类型Month

```
type Month int
const (
    January Month = 1 + iota
    February
    March
    April
    May
    June
    July
    August
    September
    October
    November
    December
)
//String返回月份的英文名"January"
func (m Month) String() string
```

### 结构体Location

```
//Location代表一个（关联到某个时间点的）地点，以及该地点所在的时区
type Location struct {
    // 内含隐藏或非导出字段
}
//Local代表系统本地，对应本地时区。
var Local *Location = &localLoc
//UTC代表通用协调时间，对应零时区。
var UTC *Location = &utcLoc

//String返回对时区信息的描述，返回值绑定为LoadLocation或FixedZone函数创建l时的name参数。
func (*Location) String ¶
```

### 函数LoadLocation

```
//LoadLocation返回使用给定的名字创建的Location
func LoadLocation(name string) (*Location, error)
```

### 函数FixedZone

```
//FixedZone使用给定的地点名name和时间偏移量offset（单位秒）创建并返回一个Location
func FixedZone(name string, offset int) *Location
```

### 结构体Time

```
//Time代表一个纳秒精度的时间点
type Time struct {
    // 内含隐藏或非导出字段
}
//Time零值代表时间点January 1, year 1, 00:00:00.000000000 UTC
```

### 函数Date

```
func Date(year int, month Month, day, hour, min, sec, nsec int, loc *Location) Time
//Date返回一个时区为loc、当地时间为
//year-month-day hour:min:sec + nsec nanoseconds
```

### 函数Now

```
func Now() Time
```

### 函数Parse

```
func Parse(layout, value string) (Time, error)
//layout定义了参考时间
//Mon Jan 2 15:04:05 -0700 MST 2006
```

### 函数ParseInLocation

```
func ParseInLocation(layout, value string, loc *Location) (Time, error)
```

### 函数Unix

```
func Unix(sec int64, nsec int64) Time
```

### 结构体Time的方法

```
func Date(year int, month Month, day, hour, min, sec, nsec int, loc *Location) Time
func Parse(layout, value string) (Time, error)
func ParseInLocation(layout, value string, loc *Location) (Time, error)
func Now() Time
func Unix(sec int64, nsec int64) Time
func (t Time) Location() *Location
func (t Time) Zone() (name string, offset int)
func (t Time) IsZero() bool
func (t Time) Local() Time
func (t Time) UTC() Time
func (t Time) In(loc *Location) Time
func (t Time) Unix() int64
func (t Time) UnixNano() int64
func (t Time) Equal(u Time) bool
func (t Time) Before(u Time) bool
func (t Time) After(u Time) bool
func (t Time) Date() (year int, month Month, day int)
func (t Time) Clock() (hour, min, sec int)
func (t Time) Year() int
func (t Time) Month() Month
func (t Time) ISOWeek() (year, week int)
func (t Time) YearDay() int
func (t Time) Day() int
func (t Time) Weekday() Weekday
func (t Time) Hour() int
func (t Time) Minute() int
func (t Time) Second() int
func (t Time) Nanosecond() int
func (t Time) Add(d Duration) Time
func (t Time) AddDate(years int, months int, days int) Time
func (t Time) Sub(u Time) Duration
func (t Time) Round(d Duration) Time
func (t Time) Truncate(d Duration) Time
func (t Time) Format(layout string) string
func (t Time) String() string
func (t Time) GobEncode() ([]byte, error)
func (t *Time) GobDecode(data []byte) error
func (t Time) MarshalBinary() ([]byte, error)
func (t *Time) UnmarshalBinary(data []byte) error
func (t Time) MarshalJSON() ([]byte, error)
func (t *Time) UnmarshalJSON(data []byte) error
func (t Time) MarshalText() ([]byte, error)
func (t *Time) UnmarshalText(data []byte) error
```

### 结构体Duration

```
type Duration int64
const (
    Nanosecond  Duration = 1
    Microsecond          = 1000 * Nanosecond
    Millisecond          = 1000 * Microsecond
    Second               = 1000 * Millisecond
    Minute               = 60 * Second
    Hour                 = 60 * Minute
)
//Duration类型值表示为某时间单元的个
second := time.Second
fmt.Print(int64(second/time.Millisecond)) // prints 1000
//要将整数个某时间单元表示为Duration类型值
seconds := 10
fmt.Print(time.Duration(seconds)*time.Second) // prints 10s
```

### 结构体Duration的方法

```
func (d Duration) Hours() float64
func (d Duration) Minutes() float64
func (d Duration) Seconds() float64
func (d Duration) Nanoseconds() int64
func (d Duration) String() string
```

### 函数ParseDuration

```
func ParseDuration(s string) (Duration, error)
```

### 函数Since

```
func Since(t Time) Duration
//
package main
func main() {
	t1 := time.Now()
	time.Sleep(time.Second*5)
	fmt.Println(time.Since(t1))
}
```

### 结构体Timer

```
type Timer struct {
    C <-chan Time
    // 内含隐藏或非导出字段
}

```

### 函数NewTimer

```
//NewTimer创建一个Timer，它会在最少过去时间段d后到期，向其自身的C字段发送当时的时间
func NewTimer(d Duration) *Timer
```

### 函数AfterFunc

```
//AfterFunc另起一个go程等待时间段d过去，然后调用f。它返回一个Timer，可以通过调用其Stop方法来取消等待和对f的调用
func AfterFunc(d Duration, f func()) *Timer
```

### 函数AfterFunc简例

```
//这函数另起goroutine,需main等待
package main
var wg sync.WaitGroup
func main() {
	t := time.NewTimer(time.Duration(time.Second * 5))
	wg.Add(1)	
	select {
	case <-t.C:
		fmt.Println("2s定时器到了")
	}	time.AfterFunc(time.Duration(time.Second*1), doAftertimer)
	wg.Wait()
}
func doAftertimer() {
	fmt.Println("1s定时器到了,自动执行")
	wg.Done()
}
```

### 结构体Timer的方法 

```
func (t *Timer) Reset(d Duration) bool
func (t *Timer) Stop() bool
```

### 结构体Ticker

```
//Ticker保管一个通道，并每隔一段时间向其传递"tick"。
type Ticker struct {
    C <-chan Time // 周期性传递时间信息的通道
    // 内含隐藏或非导出字段
}
//方法
//Stop关闭一个Ticker。在关闭后，将不会发送更多的tick信息。Stop不会关闭通道t.C，以避免从该通道的读取不正确的成功。
func (t *Ticker) Stop()
```

### 函数NewTicker及简例

```

```

```
func NewTicker(d Duration) *Ticker
//简例
package main
func tik(ctx context.Context,wg *sync.WaitGroup) {
	defer wg.Done()
	t := time.NewTicker(time.Second * 1)
	for {
		select {
		case c := <-t.C:
			fmt.Printf("%T,%v\n", c, c)
		case <-ctx.Done():
			fmt.Println("优雅退出")
			return
		}
	}
}
func main() {
	var wg sync.WaitGroup
	ctx, exit := context.WithCancel(context.Background())
	wg.Add(1)
	go tik(ctx,&wg)
	time.Sleep(time.Second*3)
	exit()
	wg.Wait()
}
```

### 函数Sleep

```
//Sleep阻塞当前go程至少d代表的时间段。d<=0时，Sleep会立刻返回。
func Sleep(d Duration)
```

### 函数After

```
//After会在另一线程经过时间段d后向返回值发送当时的时间。等价于NewTimer(d).C
func After(d Duration) <-chan Time
//
select {
case m := <-c:
    handle(m)
case <-time.After(5 * time.Minute):
    fmt.Println("timed out")
}
```

### 函数Tick

```
//Tick是NewTicker的封装，只提供对Ticker的通道的访问。如果不需要关闭Ticker，本函数就很方便。
func Tick(d Duration) <-chan Time
//简单例子
```

### 函数Tick的简例01

```
package main
func main() {
	for i:=0;i<9;i++{
		select {
		case <-time.Tick(time.Duration(time.Second)):
			fmt.Println("tick每隔1s")
		}
	}
}
```

### 函数Ticl的简例02

```
//也可以用sleep函数
package main
func tickdo(d time.Duration,f func(time.Time)){
   for t := range time.Tick(d){//返回time.Time
      f(t)
   }
}
func do(t time.Time)  {
   fmt.Println(t.Clock())
}
func main() {
   tickdo(time.Second*3,do)
}

```

函数Timer的简例03

```
//下一个零点执行TODO,在外层套for可永久执行
package main
func main() {
	now := time.Now()
	next:=now.Add(time.Hour*24)
	next= time.Date(next.Year(),next.Month(),next.Day(),0,0,0,0,next.Location())
	fmt.Println(now)
	fmt.Println(next)
	t:=time.NewTimer(next.Sub(now))
	<-t.C
	fmt.Println("零点TODO")
}
```

