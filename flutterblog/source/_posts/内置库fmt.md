---
title: go内置库fmt
date: 2021-02-05 14:46:57
tags:
    - buildin
    - fmt
categories: 内置库
top: 90
toc: true # 是否启用内容索引
sidebar: true # 是否启用sidebar侧边栏，none：不启用
---
有B语言到C语言,再有go语言
go语言的短小精悍
<!-- more -->
## package fmt

```
import "fmt"
```

mt包实现了类似C语言printf和scanf的格式化I/O。格式化动作（'verb'）源自C语言但更简单。

#### Printing

verb：

通用：

```
%v	值的默认格式表示
%+v	类似%v，但输出结构体时会添加字段名
%#v	值的Go语法表示
%T	值的类型的Go语法表示
%%	百分号
```

布尔值：

```
%t	单词true或false
```

整数：

```
%b	表示为二进制
%c	该值对应的unicode码值
%d	表示为十进制
%o	表示为八进制
%q	该值对应的单引号括起来的go语法字符字面值，必要时会采用安全的转义表示
%x	表示为十六进制，使用a-f
%X	表示为十六进制，使用A-F
%U	表示为Unicode格式：U+1234，等价于"U+%04X"
```

浮点数与复数的两个组分：

```
%b	无小数部分、二进制指数的科学计数法，如-123456p-78；参见strconv.FormatFloat
%e	科学计数法，如-1234.456e+78
%E	科学计数法，如-1234.456E+78
%f	有小数部分但无指数部分，如123.456
%F	等价于%f
%g	根据实际情况采用%e或%f格式（以获得更简洁、准确的输出）
%G	根据实际情况采用%E或%F格式（以获得更简洁、准确的输出）
```

字符串和[]byte：

```
%s	直接输出字符串或者[]byte
%q	该值对应的双引号括起来的go语法字符串字面值，必要时会采用安全的转义表示
%x	每个字节用两字符十六进制数表示（使用a-f）
%X	每个字节用两字符十六进制数表示（使用A-F）    
```

指针：

```
%p	表示为十六进制，并加上前导的0x    
```

没有%u。整数如果是无符号类型自然输出也是无符号的。类似的，也没有必要指定操作数的尺寸（int8，int64）。

宽度通过一个紧跟在百分号后面的十进制数指定，如果未指定宽度，则表示值时除必需之外不作填充。精度通过（可选的）宽度后跟点号后跟的十进制数指定。如果未指定精度，会使用默认精度；如果点号后没有跟数字，表示精度为0。举例如下：

```
%f:    默认宽度，默认精度
%9f    宽度9，默认精度
%.2f   默认宽度，精度2
%9.2f  宽度9，精度2
%9.f   宽度9，精度0    
```

宽度和精度格式化控制的是Unicode码值的数量（不同于C的printf，它的这两个因数指的是字节的数量）。两者任一个或两个都可以使用'*'号取代，此时它们的值将被对应的参数（按'*'号和verb出现的顺序，即控制其值的参数会出现在要表示的值前面）控制，这个操作数必须是int类型。

对于大多数类型的值，宽度是输出字符数目的最小数量，如果必要会用空格填充。对于字符串，精度是输出字符数目的最大数量，如果必要会截断字符串。

对于整数，宽度和精度都设置输出总长度。采用精度时表示右对齐并用0填充，而宽度默认表示用空格填充。

对于浮点数，宽度设置输出总长度；精度设置小数部分长度（如果有的话），除了%g和%G，此时精度设置总的数字个数。例如，对数字123.45，格式%6.2f 输出123.45；格式%.4g输出123.5。%e和%f的默认精度是6，%g的默认精度是可以将该值区分出来需要的最小数字个数。

对复数，宽度和精度会分别用于实部和虚部，结果用小括号包裹。因此%f用于1.2+3.4i输出(1.200000+3.400000i)。

其它flag：

```
'+'	总是输出数值的正负号；对%q（%+q）会生成全部是ASCII字符的输出（通过转义）；
' '	对数值，正数前加空格而负数前加负号；
'-'	在输出右边填充空白而不是默认的左边（即从默认的右对齐切换为左对齐）；
'#'	切换格式：
  	八进制数前加0（%#o），十六进制数前加0x（%#x）或0X（%#X），指针去掉前面的0x（%#p）；
 	对%q（%#q），如果strconv.CanBackquote返回真会输出反引号括起来的未转义字符串；
 	对%U（%#U），输出Unicode格式后，如字符可打印，还会输出空格和单引号括起来的go字面值；
  	对字符串采用%x或%X时（% x或% X）会给各打印的字节之间加空格；
'0'	使用0而不是空格填充，对于数值类型会把填充的0放在正负号后面；
```

verb会忽略不支持的flag。例如，因为没有十进制切换模式，所以%#d和%d的输出是相同的。

对每一个类似Printf的函数，都有对应的Print型函数，该函数不接受格式字符串，就效果上等价于对每一个参数都是用verb %v。另一个变体Println型函数会在各个操作数的输出之间加空格并在最后换行。

不管verb如何，如果操作数是一个接口值，那么会使用接口内部保管的值，而不是接口，因此：

```
var i interface{} = 23
fmt.Printf("%v\n", i)
```

会输出23。

除了verb %T和%p之外；对实现了特定接口的操作数会考虑采用特殊的格式化技巧。按应用优先级如下：

\1. 如果操作数实现了Formatter接口，会调用该接口的方法。Formatter提供了格式化的控制。

\2. 如果verb %v配合flag #使用（%#v），且操作数实现了GoStringer接口，会调用该接口。

如果操作数满足如下两条任一条，对于%s、%q、%v、%x、%X五个verb，将考虑：

\3. 如果操作数实现了error接口，Error方法会用来生成字符串，随后将按给出的flag（如果有）和verb格式化。

\4. 如果操作数具有String方法，这个方法将被用来生成字符串，然后将按给出的flag（如果有）和verb格式化。

复合类型的操作数，如切片和结构体，格式化动作verb递归地应用于其每一个成员，而不是作为整体一个操作数使用。因此%q会将[]string的每一个成员括起来，%6.2f会控制浮点数组的每一个元素的格式化。

为了避免可能出现的无穷递归，如：

```
type X string
func (x X) String() string { return Sprintf("<%s>", x) }
```

应在递归之前转换值的类型：

```
func (x X) String() string { return Sprintf("<%s>", string(x)) }
```

显式指定参数索引：

在Printf、Sprintf、Fprintf三个函数中，默认的行为是对每一个格式化verb依次对应调用时成功传递进来的参数。但是，紧跟在verb之前的[n]符号表示应格式化第n个参数（索引从1开始）。同样的在'*'之前的[n]符号表示采用第n个参数的值作为宽度或精度。在处理完方括号表达式[n]后，除非另有指示，会接着处理参数n+1，n+2……（就是说移动了当前处理位置）。例如：

```
fmt.Sprintf("%[2]d %[1]d\n", 11, 22)
```

会生成"22 11"，而：

```
fmt.Sprintf("%[3]*.[2]*[1]f", 12.0, 2, 6),
```

等价于：

```
fmt.Sprintf("%6.2f", 12.0),
```

会生成" 12.00"。因为显式的索引会影响随后的verb，这种符号可以通过重设索引用于多次打印同一个值：

```
fmt.Sprintf("%d %d %#[1]x %#x", 16, 17)
```

会生成"16 17 0x10 0x11"

格式化错误：

如果给某个verb提供了非法的参数，如给%d提供了一个字符串，生成的字符串会包含该问题的描述，如下所例：

```
错误的类型或未知的verb：%!verb(type=value)
	Printf("%d", hi):          %!d(string=hi)
太多参数（采用索引时会失效）：%!(EXTRA type=value)
	Printf("hi", "guys"):      hi%!(EXTRA string=guys)
太少参数: %!verb(MISSING)
	Printf("hi%d"):            hi %!d(MISSING)
宽度/精度不是整数值：%!(BADWIDTH) or %!(BADPREC)
	Printf("%*s", 4.5, "hi"):  %!(BADWIDTH)hi
	Printf("%.*s", 4.5, "hi"): %!(BADPREC)hi
没有索引指向的参数：%!(BADINDEX)
	Printf("%*[2]d", 7):       %!d(BADINDEX)
	Printf("%.[2]d", 7):       %!d(BADINDEX)
```

所有的错误都以字符串"%!"开始，有时会后跟单个字符（verb标识符），并以加小括弧的描述结束。

如果被print系列函数调用时，Error或String方法触发了panic，fmt包会根据panic重建错误信息，用一个字符串说明该panic经过了fmt包。例如，一个String方法调用了panic("bad")，生成的格式化信息差不多是这样的：

```
%!s(PANIC=bad)
```

%!s指示表示错误（panic）出现时的使用的verb。

#### Scanning

一系列类似的函数可以扫描格式化文本以生成值。

Scan、Scanf和Scanln从标准输入os.Stdin读取文本；Fscan、Fscanf、Fscanln从指定的io.Reader接口读取文本；Sscan、Sscanf、Sscanln从一个参数字符串读取文本。

Scanln、Fscanln、Sscanln会在读取到换行时停止，并要求一次提供一行所有条目；Scanf、Fscanf、Sscanf只有在格式化文本末端有换行时会读取到换行为止；其他函数会将换行视为空白。

Scanf、Fscanf、Sscanf会根据格式字符串解析参数，类似Printf。例如%x会读取一个十六进制的整数，%v会按对应值的默认格式读取。格式规则类似Printf，有如下区别：

```
%p 未实现
%T 未实现
%e %E %f %F %g %G 效果相同，用于读取浮点数或复数类型
%s %v 用在字符串时会读取空白分隔的一个片段
flag '#'和'+' 未实现   
```

在无格式化verb或verb %v下扫描整数时会接受常用的进制设置前缀0（八进制）和0x（十六进制）。

宽度会在输入文本中被使用（%5s表示最多读取5个rune来生成一个字符串），但没有使用精度的语法（没有%5.2f，只有%5f）。

当使用格式字符串进行扫描时，多个连续的空白字符（除了换行符）在输出和输出中都被等价于一个空白符。在此前提下，格式字符串中的文本必须匹配输入的文本；如果不匹配扫描会中止，函数的整数返回值说明已经扫描并填写的参数个数。

在所有的扫描函数里，\r\n都被视为\n。

在所有的扫描函数里，如果一个操作数实现了Scan方法（或者说，它实现了Scanner接口），将会使用该接口为该操作数扫描文本。另外，如果如果扫描到（准备填写）的参数比提供的参数个数少，会返回一个错误。

提供的所有参数必须为指针或者实现了Scanner接口。注意：Fscan等函数可能会在返回前多读取一个rune，这导致多次调用这些函数时可能会跳过部分输入。只有在输入里各值之间没有空白时，会出现问题。如果提供给Fscan等函数的io.Reader接口实现了ReadRune方法，将使用该方法读取字符。如果该io.Reader接口还实现了UnreadRune方法，将是使用该方法保存字符，这样可以使成功执行的Fscan等函数不会丢失数据。如果要给一个没有这两个方法的io.Reader接口提供这两个方法，使用bufio.NewReader。

### Index

[返回首页](https://studygolang.com/static/pkgdoc/main.html)

[type Stringer](https://studygolang.com/static/pkgdoc/pkg/fmt.htm#Stringer)

[type GoStringer](https://studygolang.com/static/pkgdoc/pkg/fmt.htm#GoStringer)

[type State](https://studygolang.com/static/pkgdoc/pkg/fmt.htm#State)

[type Formatter](https://studygolang.com/static/pkgdoc/pkg/fmt.htm#Formatter)

[type ScanState](https://studygolang.com/static/pkgdoc/pkg/fmt.htm#ScanState)

[type Scanner](https://studygolang.com/static/pkgdoc/pkg/fmt.htm#Scanner)

[func Printf(format string, a ...interface{}) (n int, err error)](https://studygolang.com/static/pkgdoc/pkg/fmt.htm#Printf)

[func Fprintf(w io.Writer, format string, a ...interface{}) (n int, err error)](https://studygolang.com/static/pkgdoc/pkg/fmt.htm#Fprintf)

[func Sprintf(format string, a ...interface{}) string](https://studygolang.com/static/pkgdoc/pkg/fmt.htm#Sprintf)

[func Print(a ...interface{}) (n int, err error)](https://studygolang.com/static/pkgdoc/pkg/fmt.htm#Print)

[func Fprint(w io.Writer, a ...interface{}) (n int, err error)](https://studygolang.com/static/pkgdoc/pkg/fmt.htm#Fprint)

[func Sprint(a ...interface{}) string](https://studygolang.com/static/pkgdoc/pkg/fmt.htm#Sprint)

[func Println(a ...interface{}) (n int, err error)](https://studygolang.com/static/pkgdoc/pkg/fmt.htm#Println)

[func Fprintln(w io.Writer, a ...interface{}) (n int, err error)](https://studygolang.com/static/pkgdoc/pkg/fmt.htm#Fprintln)

[func Sprintln(a ...interface{}) string](https://studygolang.com/static/pkgdoc/pkg/fmt.htm#Sprintln)

[func Errorf(format string, a ...interface{}) error](https://studygolang.com/static/pkgdoc/pkg/fmt.htm#Errorf)

[func Scanf(format string, a ...interface{}) (n int, err error)](https://studygolang.com/static/pkgdoc/pkg/fmt.htm#Scanf)

[func Fscanf(r io.Reader, format string, a ...interface{}) (n int, err error)](https://studygolang.com/static/pkgdoc/pkg/fmt.htm#Fscanf)

[func Sscanf(str string, format string, a ...interface{}) (n int, err error)](https://studygolang.com/static/pkgdoc/pkg/fmt.htm#Sscanf)

[func Scan(a ...interface{}) (n int, err error)](https://studygolang.com/static/pkgdoc/pkg/fmt.htm#Scan)

[func Fscan(r io.Reader, a ...interface{}) (n int, err error)](https://studygolang.com/static/pkgdoc/pkg/fmt.htm#Fscan)

[func Sscan(str string, a ...interface{}) (n int, err error)](https://studygolang.com/static/pkgdoc/pkg/fmt.htm#Sscan)

[func Scanln(a ...interface{}) (n int, err error)](https://studygolang.com/static/pkgdoc/pkg/fmt.htm#Scanln)

[func Fscanln(r io.Reader, a ...interface{}) (n int, err error)](https://studygolang.com/static/pkgdoc/pkg/fmt.htm#Fscanln)

[func Sscanln(str string, a ...interface{}) (n int, err error)](https://studygolang.com/static/pkgdoc/pkg/fmt.htm#Sscanln)

### type [Stringer](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#63)

```
type Stringer interface {
    String() string
}
```

实现了Stringer接口的类型（即有String方法），定义了该类型值的原始显示。当采用任何接受字符的verb（%v %s %q %x %X）动作格式化一个操作数时，或者被不使用格式字符串如Print函数打印操作数时，会调用String方法来生成输出的文本。

### type [GoStringer](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#71)

```
type GoStringer interface {
    GoString() string
}
```

实现了GoStringer接口的类型（即有GoString方法），定义了该类型值的go语法表示。当采用verb %#v格式化一个操作数时，会调用GoString方法来生成输出的文本。

### type [State](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#39)

```
type State interface {
    // Write方法用来写入格式化的文本
    Write(b []byte) (ret int, err error)
    // Width返回宽度值，及其是否被设置
    Width() (wid int, ok bool)
    // Precision返回精度值，及其是否被设置
    Precision() (prec int, ok bool)
    // Flag报告是否设置了flag c（一个字符，如+、-、#等）
    Flag(c int) bool
}
```

State代表一个传递给自定义Formatter接口的Format方法的打印环境。它实现了io.Writer接口用来写入格式化的文本，还提供了该操作数的格式字符串指定的选项和宽度、精度信息（通过调用方法）。

### type [Formatter](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#54)

```
type Formatter interface {
    // c为verb，f提供verb的细节信息和Write方法用于写入生成的格式化文本
    Format(f State, c rune)
}
```

实现了Formatter接口的类型可以定制自己的格式化输出。Format方法的实现内部可以调用Sprint或Fprint等函数来生成自身的输出。

### type [ScanState](https://github.com/golang/go/blob/master/src/fmt/scan.go?name=release#29)

```
type ScanState interface {
    // 从输入读取下一个rune（Unicode码值），在读取超过指定宽度时会返回EOF
    // 如果在Scanln、Fscanln或Sscanln中被调用，本方法会在返回第一个'\n'后再次调用时返回EOF
    ReadRune() (r rune, size int, err error)
    // UnreadRune方法让下一次调用ReadRune时返回上一次返回的rune且不移动读取位置
    UnreadRune() error
    // SkipSpace方法跳过输入中的空白，换行被视为空白
    // 在Scanln、Fscanln或Sscanln中被调用时，换行被视为EOF
    SkipSpace()
    // 方法从输入中依次读取rune并用f测试，直到f返回假；将读取的rune组织为一个[]byte切片返回。
    // 如果skipSpace参数为真，本方法会先跳过输入中的空白。
    // 如果f为nil，会使用!unicode.IsSpace(c)；就是说返回值token将为一串非空字符。
    // 换行被视为空白，在Scanln、Fscanln或Sscanln中被调用时，换行被视为EOF。
    // 返回的切片指向一个共享内存，可能被下一次调用Token方法时重写；
    // 或被使用该Scanstate的另一个Scan函数重写；或者在本次调用的Scan方法返回时重写。
    Token(skipSpace bool, f func(rune) bool) (token []byte, err error)
    // Width返回返回宽度值，及其是否被设置。单位是unicode码值。
    Width() (wid int, ok bool)
    // 因为本接口实现了ReadRune方法，Read方法永远不应被在Scanner接口中使用。
    // 一个合法的ScanStat接口实现可能会选择让本方法总是返回错误。
    Read(buf []byte) (n int, err error)
}
```

ScanState代表一个将传递给Scanner接口的Scan方法的扫描环境。 Scan函数中，可以进行一次一个rune的扫描，或者使用Token方法获得下一个token（比如空白分隔的token）。

### type [Scanner](https://github.com/golang/go/blob/master/src/fmt/scan.go?name=release#63)

```
type Scanner interface {
    Scan(state ScanState, verb rune) error
}
```

当Scan、Scanf、Scanln或类似函数接受实现了Scanner接口的类型（其Scan方法的receiver必须是指针，该方法从输入读取该类型值的字符串表示并将结果写入receiver）作为参数时，会调用其Scan方法进行定制的扫描。

### func [Printf](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#196)

```
func Printf(format string, a ...interface{}) (n int, err error)
```

Printf根据format参数生成格式化的字符串并写入标准输出。返回写入的字节数和遇到的任何错误。

### func [Fprintf](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#186)

```
func Fprintf(w io.Writer, format string, a ...interface{}) (n int, err error)
```

Fprintf根据format参数生成格式化的字符串并写入w。返回写入的字节数和遇到的任何错误。

### func [Sprintf](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#201)

```
func Sprintf(format string, a ...interface{}) string
```

Sprintf根据format参数生成格式化的字符串并返回该字符串。

### func [Print](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#231)

```
func Print(a ...interface{}) (n int, err error)
```

Print采用默认格式将其参数格式化并写入标准输出。如果两个相邻的参数都不是字符串，会在它们的输出之间添加空格。返回写入的字节数和遇到的任何错误。

### func [Fprint](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#220)

```
func Fprint(w io.Writer, a ...interface{}) (n int, err error)
```

Fprint采用默认格式将其参数格式化并写入w。如果两个相邻的参数都不是字符串，会在它们的输出之间添加空格。返回写入的字节数和遇到的任何错误。

### func [Sprint](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#237)

```
func Sprint(a ...interface{}) string
```

Sprint采用默认格式将其参数格式化，串联所有输出生成并返回一个字符串。如果两个相邻的参数都不是字符串，会在它们的输出之间添加空格。

### func [Println](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#263)

```
func Println(a ...interface{}) (n int, err error)
```

Println采用默认格式将其参数格式化并写入标准输出。总是会在相邻参数的输出之间添加空格并在输出结束后添加换行符。返回写入的字节数和遇到的任何错误。

### func [Fprintln](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#252)

```
func Fprintln(w io.Writer, a ...interface{}) (n int, err error)
```

Fprintln采用默认格式将其参数格式化并写入w。总是会在相邻参数的输出之间添加空格并在输出结束后添加换行符。返回写入的字节数和遇到的任何错误。

### func [Sprintln](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#269)

```
func Sprintln(a ...interface{}) string
```

Sprintln采用默认格式将其参数格式化，串联所有输出生成并返回一个字符串。总是会在相邻参数的输出之间添加空格并在输出结束后添加换行符。

### func [Errorf](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#211)

```
func Errorf(format string, a ...interface{}) error
```

Errorf根据format参数生成格式化字符串并返回一个包含该字符串的错误。

### func [Scanf](https://github.com/golang/go/blob/master/src/fmt/scan.go?name=release#84)

```
func Scanf(format string, a ...interface{}) (n int, err error)
```

Scanf从标准输入扫描文本，根据format 参数指定的格式将成功读取的空白分隔的值保存进成功传递给本函数的参数。返回成功扫描的条目个数和遇到的任何错误。

### func [Fscanf](https://github.com/golang/go/blob/master/src/fmt/scan.go?name=release#143)

```
func Fscanf(r io.Reader, format string, a ...interface{}) (n int, err error)
```

Fscanf从r扫描文本，根据format 参数指定的格式将成功读取的空白分隔的值保存进成功传递给本函数的参数。返回成功扫描的条目个数和遇到的任何错误。

### func [Sscanf](https://github.com/golang/go/blob/master/src/fmt/scan.go?name=release#116)

```
func Sscanf(str string, format string, a ...interface{}) (n int, err error)
```

Sscanf从字符串str扫描文本，根据format 参数指定的格式将成功读取的空白分隔的值保存进成功传递给本函数的参数。返回成功扫描的条目个数和遇到的任何错误。

### func [Scan](https://github.com/golang/go/blob/master/src/fmt/scan.go?name=release#71)

```
func Scan(a ...interface{}) (n int, err error)
```

Scan从标准输入扫描文本，将成功读取的空白分隔的值保存进成功传递给本函数的参数。换行视为空白。返回成功扫描的条目个数和遇到的任何错误。如果读取的条目比提供的参数少，会返回一个错误报告原因。

### func [Fscan](https://github.com/golang/go/blob/master/src/fmt/scan.go?name=release#124)

```
func Fscan(r io.Reader, a ...interface{}) (n int, err error)
```

Fscan从r扫描文本，将成功读取的空白分隔的值保存进成功传递给本函数的参数。换行视为空白。返回成功扫描的条目个数和遇到的任何错误。如果读取的条目比提供的参数少，会返回一个错误报告原因。

### func [Sscan](https://github.com/golang/go/blob/master/src/fmt/scan.go?name=release#103)

```
func Sscan(str string, a ...interface{}) (n int, err error)
```

Sscan从字符串str扫描文本，将成功读取的空白分隔的值保存进成功传递给本函数的参数。换行视为空白。返回成功扫描的条目个数和遇到的任何错误。如果读取的条目比提供的参数少，会返回一个错误报告原因。

### func [Scanln](https://github.com/golang/go/blob/master/src/fmt/scan.go?name=release#77)

```
func Scanln(a ...interface{}) (n int, err error)
```

Scanln类似Scan，但会在换行时才停止扫描。最后一个条目后必须有换行或者到达结束位置。

### func [Fscanln](https://github.com/golang/go/blob/master/src/fmt/scan.go?name=release#133)

```
func Fscanln(r io.Reader, a ...interface{}) (n int, err error)
```

Fscanln类似Fscan，但会在换行时才停止扫描。最后一个条目后必须有换行或者到达结束位置。

### func [Sscanln](https://github.com/golang/go/blob/master/src/fmt/scan.go?name=release#109)

```
func Sscanln(str string, a ...interface{}) (n int, err error)
```

Sscanln类似Sscan，但会在换行时才停止扫描。最后一个条目后必须有换行或者到达结束位置。