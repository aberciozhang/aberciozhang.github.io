---
title: 库rpc
date: 2021-02-05 14:46:57
tags:
    - rpc
    - grpc
categories: 内置库
top: 80
toc: true # 是否启用内容索引
sidebar: true # 是否启用sidebar侧边栏，none：不启用
---
go内置rpc
rpc的基础使用
最简单的例子
<!-- more -->
## 库rpc

## 例子1服务端

定义结构体和方法->main中注册该服务->设置端口->连接监听,启动服务连接

```
package main
type SayService struct{}
func (s *SayService) Say(request string, reply *string) error {	*reply = "这是服务端 " + request
	return nil}
func main() {
	_ = rpc.RegisterName("SayService", new(SayService))
	listen,err := net.Listen("tcp",":1234")
	if err!=nil{log.Fatal("监听tcp错误",err)}
	conn,err := listen.Accept()
	rpc.ServeConn(conn)
}
```

## 例子1客户端

起动连接->设置参数reply->call服务端方法

```
package main
func main() {
	client,err := rpc.Dial("tcp","localhost:1234")
	if err!=nil {log.Fatal("连服务端失败",err)}
	var reply string
	err = client.Call("SayService.Say","+来自客户端",&reply)
	if err != nil {log.Fatal(err)}
	fmt.Println(reply)
}
```

## 例子2服务端

```
package main//服务端
const HelloServiceName = "HelloService"
type HelloServiceInterface = interface {Hello(request string, reply *string) error}
func RegisterHelloService(svc HelloServiceInterface) error {
	return rpc.RegisterName(HelloServiceName, svc)}
type HelloService struct{}
func (p *HelloService) Hello(request string, reply *string) error {
	*reply = "来自服务端" + request
	return nil}
func main() {
	_ = RegisterHelloService(new(HelloService))
	listen, err := net.Listen("tcp", ":1234")
	if err != nil {log.Fatal("tcp错误", err)}
	for {	conn, err := listen.Accept()
		if err != nil {log.Fatal("accept错误", err)}
		go rpc.ServeConn(conn)	}}
```

## 例子2客户端

```
package main//客户端
type HelloServiceClient struct{ *rpc.Client }
//var _ HelloServiceInterface = (*HelloServiceClient)(nil)
func DialHelloService(network, address string) (*HelloServiceClient, error) {
	c, err := rpc.Dial(network, address)
	if err != nil {return nil, err}
	return &HelloServiceClient{Client: c}, nil}
func (p *HelloServiceClient) Hello(request string, reply *string) error {
	return p.Client.Call("HelloService.Hello", request, reply)}
func main() {
	client, err := DialHelloService("tcp", ":1234")
	if err != nil {log.Fatal("连接服务端失败", err)}
	var reply string
	err = client.Hello("+来自客户端", &reply)
	if err != nil {log.Fatal(err)}
	fmt.Println(reply)}
```

## 库rpc的常量和变量

```
var DefaultServer = NewServer()
const (
	// Defaults used by HandleHTTP
	DefaultRPCPath   = "/_goRPC_"
	DefaultDebugPath = "/debug/rpc"
)
var typeOfError = reflect.TypeOf((*error)(nil)).Elem()
var connected = "200 Connected to Go RPC"
var DefaultServer = NewServer()
```

## 库rpc的函数RegisterName

```
func RegisterName(name string, rcvr interface{}) error {
	return DefaultServer.RegisterName(name, rcvr)
}
```

## 库rpc的函数Register

```
func Register(rcvr interface{}) error { return DefaultServer.Register(rcvr) }
```

## 库rpc的函数NewServer

```
func NewServer() *Server {
   return &Server{}
}
```

## 库rpc的Server结构体

```
type Server struct {
	serviceMap sync.Map   // map[string]*service
	reqLock    sync.Mutex // protects freeReq
	freeReq    *Request
	respLock   sync.Mutex // protects freeResp
	freeResp   *Response
}
```

## 库rpc的Request结构体

```
type Request struct {
   ServiceMethod string   // format: "Service.Method"
   Seq           uint64  //sequence number chosen client
   next          *Request // for free list in Server
}
```

## 库rpc的Response结构体

```
type Response struct {
   ServiceMethod string    // echoes that of the Request
   Seq           uint64    // echoes that of the request
   Error         string    // error, if any.
   next          *Response // for free list in Server
}
```

## 库rpc的service结构体

```
type service struct {
   name   string                 
   rcvr reflect.Value// receiver of methods for the service
   typ    reflect.Type   // type of the receiver
   method map[string]*methodType // registered methods
}
func (s *service) call(server *Server, sending *sync.Mutex, wg *sync.WaitGroup, mtype *methodType, req *Request, argv, replyv reflect.Value, codec ServerCodec) {..}
```

## 库rpc的methodType结构体

```
type methodType struct {
   sync.Mutex // protects counters
   method     reflect.Method
   ArgType    reflect.Type
   ReplyType  reflect.Type
   numCalls   uint
}
func (m *methodType) NumCalls() (n uint) {
	m.Lock()
	n = m.numCalls
	m.Unlock()
	return n
}
```

## 库rpc的ServerCodec接口

```
type ServerCodec interface {
   ReadRequestHeader(*Request) error
   ReadRequestBody(interface{}) error
   WriteResponse(*Response, interface{}) error

   // Close can be called multiple times and must be idempotent.
   Close() error
}
```

## 库rpc的结构Server的方法

```
func (server *Server) Register(rcvr interface{}) error {
   return server.register(rcvr, "", false)
}
func (server *Server) RegisterName(name string, rcvr interface{}) error {
	return server.register(rcvr, name, true)
}
func (server *Server) register(rcvr interface{}, name string, useName bool) error {..}
func (server *Server) sendResponse(sending *sync.Mutex, req *Request, reply interface{}, codec ServerCodec, errmsg string) {..}
func (server *Server) ServeConn(conn io.ReadWriteCloser) {.}
func (server *Server) ServeCodec(codec ServerCodec) {.}
func (server *Server) ServeRequest(codec ServerCodec) error {..}
func (server *Server) getRequest() *Request {..}
func (server *Server) freeRequest(req *Request) {
	server.reqLock.Lock()
	req.next = server.freeReq
	server.freeReq = req
	server.reqLock.Unlock()
}
func (server *Server) getResponse() *Response {.}
func (server *Server) freeResponse(resp *Response) {
	server.respLock.Lock()
	resp.next = server.freeResp
	server.freeResp = resp
	server.respLock.Unlock()
}
func (server *Server) ServeHTTP(w http.ResponseWriter, req *http.Request) {.}
func (server *Server) HandleHTTP(rpcPath, debugPath string) {
	http.Handle(rpcPath, server)
	http.Handle(debugPath, debugHTTP{server})
}
func (server *Server) readRequest(codec ServerCodec) (service *service, mtype *methodType, req *Request, argv, replyv reflect.Value, keepReading bool, err error) {.}
func (server *Server) readRequestHeader(codec ServerCodec) (svc *service, mtype *methodType, req *Request, keepReading bool, err error) {.}
func (server *Server) Accept(lis net.Listener) {
	for {
		conn, err := lis.Accept()
		if err != nil {
			log.Print("rpc.Serve: accept:", err.Error())
			return
		}
		go server.ServeConn(conn)
	}
}
```

## 库rpc的结构gobServerCodec

```
type gobServerCodec struct {
   rwc    io.ReadWriteCloser
   dec    *gob.Decoder
   enc    *gob.Encoder
   encBuf *bufio.Writer
   closed bool
}
func (c *gobServerCodec) ReadRequestHeader(r *Request) error {
	return c.dec.Decode(r)
}

func (c *gobServerCodec) ReadRequestBody(body interface{}) error {
	return c.dec.Decode(body)
}
func (c *gobServerCodec) WriteResponse(r *Response, body interface{}) (err error) {..}
func (c *gobServerCodec) Close() error {..}


```

## 库rpc的函数ServeConn

```
func ServeConn(conn io.ReadWriteCloser) {
   DefaultServer.ServeConn(conn)
}
```

## 库rpc的函数ServeCodec

```
func ServeCodec(codec ServerCodec) {
   DefaultServer.ServeCodec(codec)
}
```

## 库rpc的函数ServeRequest

```
func ServeRequest(codec ServerCodec) error {
   return DefaultServer.ServeRequest(codec)
}
```

## 库rpc的函数Accept

```
func Accept(lis net.Listener) { DefaultServer.Accept(lis) }
```

## 库rpc的函数HandleHTTP

```
func HandleHTTP() {
   DefaultServer.HandleHTTP(DefaultRPCPath, DefaultDebugPath)
}
```