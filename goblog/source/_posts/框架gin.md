---
title: 框架gin
date: 2021-02-05 14:46:57
tags:
    - http
    - gin
categories: 网络
top: 90
toc: true # 是否启用内容索引
sidebar: true # 是否启用sidebar侧边栏，none：不启用
---
gin框架速度快
性能稳定
<!-- more -->
## 框架gin

Gin 是一个用 Go (Golang) 编写的 HTTP web 框架。 它是一个类似于 [martini](https://github.com/go-martini/martini) 但拥有更好性能的 API 框架, 优于 [httprouter](https://github.com/julienschmidt/httprouter)，速度提高了近 40 倍。如果你需要极好的性能，使用 Gin

##### 主要功能

常量变量

```
// EnvGinMode indicates environment name for gin mode.
const EnvGinMode = "GIN_MODE"
const (
	// DebugMode indicates gin mode is debug.
	DebugMode = "debug"
	// ReleaseMode indicates gin mode is release.
	ReleaseMode = "release"
	// TestMode indicates gin mode is test.
	TestMode = "test"
)
const (
	debugCode = iota
	releaseCode
	testCode
)
var DefaultWriter io.Writer = os.Stdout
var DefaultErrorWriter io.Writer = os.Stderr
var ginMode = debugCode
var modeName = DebugMode
```

## 资料参考

```
https://gin-gonic.com/zh-cn/
https://github.com/gin-gonic/gin
```

## 安装导包

```
go get -u github.com/gin-gonic/gin
import "github.com/gin-gonic/gin"
import "net/http"//可能需要,自动会导
```

## 最简单的入门

```
//浏览器中访问 http://localhost:8080/ping
package main
import "github.com/gin-gonic/gin"
func main() {
    r := gin.Default()
    r.GET("/ping", func(c *gin.Context) {
        c.JSON(200, gin.H{
            "message": "pong",
        })
    })
    r.Run() // listen and serve on 0.0.0.0:8080
}
```

```
package main
import (
	"fmt"
	"github.com/gin-gonic/gin"
	"log"
)
func main() {
	engine := gin.Default()
	engine.GET("/", func(context *gin.Context) {
		fmt.Println("请求路径",context.FullPath())		context.Writer.Write([]byte("hello,world!"))
	})
	if err:=engine.Run();err!=nil{
		log.Fatal(err.Error())
	}
}
```

## GET, POST, PUT, PATCH, DELETE, OPTIONS

```
func main() {
    // Disable Console Color
    // gin.DisableConsoleColor()
    // 使用默认中间件创建一个gin路由器
    // logger and recovery (crash-free) 中间件
    router := gin.Default()
    router.GET("/someGet", getting)
    router.POST("/somePost", posting)
    router.PUT("/somePut", putting)
    router.DELETE("/someDelete", deleting)
    router.PATCH("/somePatch", patching)
    router.HEAD("/someHead", head)
    router.OPTIONS("/someOptions", options)
    // 默认启动的是 8080端口，也可以自己定义启动端口
    router.Run()
    // router.Run(":3000") for a hard coded port
}
```

## 库http的常数

```
package http
const (
	MethodGet     = "GET"
	MethodHead    = "HEAD"
	MethodPost    = "POST"
	MethodPut     = "PUT"
	MethodPatch   = "PATCH" // RFC 5789
	MethodDelete  = "DELETE"
	MethodConnect = "CONNECT"
	MethodOptions = "OPTIONS"
	MethodTrace   = "TRACE"
)
```

## 路由组

```
package main
import "github.com/gin-gonic/gin"
func main() {
	router := gin.Default()
	v1 := router.Group("/v1")
	{
		v1.GET("/login", handle)
		v1.GET("/submit", handle)
		v1.GET("/read", handle2)
	}
	v2 := router.Group("/v2")
	{
		v2.GET("/login", handle)
		v2.GET("/submit", handle)
		v2.GET("/read", handle)
	}
	_ = router.Run(":8080")
}
func handle(c *gin.Context) {//json
	c.JSON(200, gin.H{
		"message": "pong",
	})
}
func handle2(c *gin.Context) {//字符
	_,_= c.Writer.Write([]byte("hi!"))
}
```

## 日志

```
package main
import (
	"github.com/gin-gonic/gin"
	"io"
	"os"
)
func main() {
	// 禁用控制台颜色
	gin.DisableConsoleColor()
	// 创建记录日志的文件
	f, _ := os.Create("gin.log")
	gin.DefaultWriter = io.MultiWriter(f)
	// 如果需要将日志同时写入文件和控制台，请使用以下代码
	// gin.DefaultWriter = io.MultiWriter(f, os.Stdout)
	router := gin.Default()
	router.GET("/", func(c *gin.Context) {
		c.String(200, "pong")
	})
	router.Run(":8080")
}
```

## 库gin的变量

```
var DefaultWriter io.Writer = os.Stdout
var DefaultErrorWriter io.Writer = os.Stderr
```

## 库io的函数MultiWriter

```
func MultiWriter(writers ...Writer) Writer {
	allWriters := make([]Writer, 0, len(writers))
	for _, w := range writers {
		if mw, ok := w.(*multiWriter); ok {
			allWriters = append(allWriters, mw.writers...)
		} else {
			allWriters = append(allWriters, w)
		}
	}
	return &multiWriter{allWriters}
}
```

## 库gin的Context struct

```
type Context struct {
	writermem responseWriter
	Request   *http.Request
	Writer    ResponseWriter//可以在浏览器中显示
	Params   Params
	handlers HandlersChain
	index    int8
	fullPath string
	engine *Engine
	// This mutex protect Keys map
	mu sync.RWMutex
	// Keys is a key/value pair exclusively for the context of each request.
	Keys map[string]interface{}
	// Errors is a list of errors attached to all the handlers/middlewares who used this context.
	Errors errorMsgs
	// Accepted defines a list of manually accepted formats for content negotiation.
	Accepted []string
	// queryCache use url.ParseQuery cached the param query result from c.Request.URL.Query()
	queryCache url.Values
	// formCache use url.ParseQuery cached PostForm contains the parsed form data from POST, PATCH,
	// or PUT body parameters.
	formCache url.Values
	// SameSite allows a server to define a cookie attribute making it impossible for
	// the browser to send this cookie along with cross-site requests.
	sameSite http.SameSite
}
```

## 库gin的结构体Context的方法JSON

```
func (c *Context) JSON(code int, obj interface{}) {
	c.Render(code, render.JSON{Data: obj})
}
```

## 库gin的结构体Context的方法String

```
// String writes the given string into the response body.
func (c *Context) String(code int, format string, values ...interface{}) {
	c.Render(code, render.String{Format: format, Data: values})
}
```

## 库gin的结构体Context的方法ProtoBuf

```
// ProtoBuf serializes the given struct as ProtoBuf into the response body.
func (c *Context) ProtoBuf(code int, obj interface{}) {
	c.Render(code, render.ProtoBuf{Data: obj})
}
```

## 结构responseWriter的方法Write

```
func (w *responseWriter) Write(data []byte) (n int, err error) {
	w.WriteHeaderNow()
	n, err = w.ResponseWriter.Write(data)
	w.size += n
	return
}
```

## type responseWriter struct

```
type responseWriter struct {
	http.ResponseWriter
	size   int
	status int
}
```

## 结构体responseWriter的方法Write

```
func (w *responseWriter) Write(data []byte) (n int, err error) {
	w.WriteHeaderNow()
	n, err = w.ResponseWriter.Write(data)
	w.size += n
	return
}
```

## 库gin的type H

```
// H is a shortcut for map[string]interface{}
type H map[string]interface{}
```

## 库gin的type的Engine

```
type Engine struct {
	RouterGroup
	RedirectTrailingSlash bool
	RedirectFixedPath bool
	HandleMethodNotAllowed bool
	ForwardedByClientIP    bool
	AppEngine bool
	UseRawPath bool
	UnescapePathValues bool
	MaxMultipartMemory int64
	RemoveExtraSlash bool
	delims           render.Delims
	secureJsonPrefix string
	HTMLRender       render.HTMLRender
	FuncMap          template.FuncMap
	allNoRoute       HandlersChain
	allNoMethod      HandlersChain
	noRoute          HandlersChain
	noMethod         HandlersChain
	pool             sync.Pool
	trees            methodTrees
}
```

## Engine的方法Run

```
func (engine *Engine) Run(addr ...string) (err error) {
   defer func() { debugPrintError(err) }()

   address := resolveAddress(addr)
   debugPrint("Listening and serving HTTP on %s\n", address)
   err = http.ListenAndServe(address, engine)
   return
}
```

## 库gin的变量

```
var DefaultWriter io.Writer = os.Stdout
var DefaultErrorWriter io.Writer = os.Stderr
var ginMode = debugCode
var modeName = DebugMode

```

## 库gin的init函数

```
func init() {
	mode := os.Getenv(EnvGinMode)
	SetMode(mode)
}
```

## 库gin的SetMode函数

```
func SetMode(value string) {
   switch value {
   case DebugMode, "":
      ginMode = debugCode
   case ReleaseMode:
      ginMode = releaseCode
   case TestMode:
      ginMode = testCode
   default:
      panic("gin mode unknown: " + value)
   }
   if value == "" {
      value = DebugMode
   }
   modeName = value
}
```

gin的函数Mode

```
func Mode() string {
   return modeName
}
```

## gin的函数Default

```
func Default() *Engine {
	debugPrintWARNINGDefault()
	engine := New()
	engine.Use(Logger(), Recovery())
	return engine
}
```

## gin的函数New

```
func New() *Engine {
	debugPrintWARNINGNew()
	engine := &Engine{
		RouterGroup: RouterGroup{
			Handlers: nil,
			basePath: "/",
			root:     true,
		},
		FuncMap:                template.FuncMap{},
		RedirectTrailingSlash:  true,
		RedirectFixedPath:      false,
		HandleMethodNotAllowed: false,
		ForwardedByClientIP:    true,
		AppEngine:              defaultAppEngine,
		UseRawPath:             false,
		RemoveExtraSlash:       false,
		UnescapePathValues:     true,
		MaxMultipartMemory:     defaultMultipartMemory,
		trees:                  make(methodTrees, 0, 9),
		delims:                 render.Delims{Left: "{{", Right: "}}"},
		secureJsonPrefix:       "while(1);",
	}
	engine.RouterGroup.engine = engine
	engine.pool.New = func() interface{} {
		return engine.allocateContext()
	}
	return engine
}
```

## gin的函数SetMode

```
// SetMode sets gin mode according to input string.
func SetMode(value string) {
	switch value {
	case DebugMode, "":
		ginMode = debugCode
	case ReleaseMode:
		ginMode = releaseCode
	case TestMode:
		ginMode = testCode
	default:
		panic("gin mode unknown: " + value)
	}
	if value == "" {
		value = DebugMode
	}
	modeName = value
}
```



## 库gin的type RouteInfo

```
// RouteInfo represents a request route's specification which contains method and path and its handler.
type RouteInfo struct {
	Method      string
	Path        string
	Handler     string
	HandlerFunc HandlerFunc
}
// RoutesInfo defines a RouteInfo array.
type RoutesInfo []RouteInfo
```

## 库gin的结构Engine的LoadHTMLFiles方法

```
// LoadHTMLFiles loads a slice of HTML files
// and associates the result with HTML renderer.
func (engine *Engine) LoadHTMLFiles(files ...string) {
	if IsDebugging() {
		engine.HTMLRender = render.HTMLDebug{Files: files, FuncMap: engine.FuncMap, Delims: engine.delims}
		return
	}

	templ := template.Must(template.New("").Delims(engine.delims.Left, engine.delims.Right).Funcs(engine.FuncMap).ParseFiles(files...))
	engine.SetHTMLTemplate(templ)
}
```

## 库gin的结构Engine的SetHTMLTemplate方法

```
// SetHTMLTemplate associate a template with HTML renderer.
func (engine *Engine) SetHTMLTemplate(templ *template.Template) {
   if len(engine.trees) > 0 {
      debugPrintWARNINGSetHTMLTemplate()
   }

   engine.HTMLRender = render.HTMLProduction{Template: templ.Funcs(engine.FuncMap)}
}
```

## 库template

## 库template的结构Template

```
type Template struct {
	escapeErr error
	text *template.Template
	Tree       *parse.Tree
	*nameSpace // common to all associated templates
}
```

