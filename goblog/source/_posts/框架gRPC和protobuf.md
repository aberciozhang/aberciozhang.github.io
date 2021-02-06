---
title: 框架gRPC和protobuf
tags:
  - gRPC
  - protobuf
categories: 微服务
copyright: true
top: 1
toc: true
sidebar: true
date: 2021-02-06 11:48:18
---
摘要:
    go语言的短小精悍
    人生苦短,Let's Go!
<!-- more -->
## 框架gRPC

##### 安装

```
go get -u -v github.com/golang/protobuf/proto
go get -u -v google.golang.org/grpc

go get github.com/golang/protobuf/protoc-gen-go
```



## Protobuf

##### 1.下载安装exe

```
https://github.com/protocolbuffers/protobuf/releases
然后将解压后bin内的proto.exe放在go的bin内
```

2.安装protoc-gen-go

```
go get github.com/golang/protobuf/protoc-gen-go
```

##### 3.go内get grpc

```
go get -u -v google.golang.org/grpc
```

4

```
protoc.exe --go_out=plugins=grpc:. add.proto

protoc.exe --go_out=plugins=grpc:../services prod.proto

```

## 例子1的protobuf

```
//protoc --go_out=plugins=grpc:. prod.proto
//再同目录下生成文件prod.pb.go,文件夹和包名一致
syntax = "proto3";
package services;
message ProdRequest{
    int32 prod_id = 1;
}
message ProdResponse{
    int32 prod_stock = 1;
}
service ProdService{
    rpc GetProdStock(ProdRequest) returns (ProdResponse);
}
```

## 例子1的server端

```
package services//prodService.go
import 	"context"
type ProdService struct {}
func (this *ProdService)GetProdStock(ctx context.Context, request *ProdRequest) (*ProdResponse, error)  {
	return &ProdResponse{ProdStock:20},nil
}
```

## 例子1的服务端主程序

```
package main//service.go
import (
	"allstudy/gaoji/10grpc/services"
	"google.golang.org/grpc"
	"net"
)
func main()  {
	rpcServer := grpc.NewServer()	services.RegisterProdServiceServer(rpcServer,new(services.ProdService))
	listener, _ := net.Listen("tcp", ":8081")
	rpcServer.Serve(listener)
}
```

## 例子1的客户端主程序

```
package main//client.go
import (
	"allstudy/gaoji/10grpc/services"
	"context"
	"google.golang.org/grpc"
)
func main()  {
	 conn,err := grpc.Dial(":8081",grpc.WithInsecure())
	if err != nil {log.Fatal(err)}
	defer conn.Close()
	 prodClient := services.NewProdServiceClient(conn)
	 prodRes,err := prodClient.GetProdStock(context.Background(),&services.ProdRequest{ProdId:19})
	if err != nil {	log.Fatal(err)}
	fmt.Println(prodRes.ProdStock)
}
```

## 例子2的proto的文件

```
protoc --go_out=plugins=grpc:. user.proto
protoc -I . --go_out=plugins=grpc:. ./user.proto
```

```
//版本号
syntax = "proto3";
//指定包名
package proto;
//参数结构体
message UserRequest{
    string name = 1;
}
//响应的结构体
message UserResponse{
    int32 id = 1;
    string name = 2;
    int32 age = 3;
    //repeated为可变数组
    repeated string hobby = 4;
}
//service定义方法
service UserInfoService{
    rpc GetUserInfo(UserRequest) returns (UserResponse);
}
```

## 例子2的服务端

```
package main
import "context"
//定义空接口
type UserInfoService struct {}
var u = UserInfoService{}
//实现方法
func (u *UserInfoService)GetUserInfo(ctx context.Context, request *proto.UserRequest) (*proto.UserResponse, error){
	//通过用户名查询用户信息
	//var resp *proto.UserResponse
	name := request.Name
	//数据库里查询信息通过name
	if name == "zs"{
		resp := &proto.UserResponse{
			Id:1,
			Name:name,
			Age:100,
			Hobby:[]string{"sing","run","other"},
		}
		return resp,nil
	}
	return nil,nil
}//上面可以封装到serverproto到proto.pb.go一起文件夹中
func main()  {
	//监听
	listener, err := net.Listen("tcp", "localhost:8080")
	if err != nil {fmt.Println("监听异常",err)}
	//实例化grpc
	server := grpc.NewServer()
	//在grpc上注册微服务
	proto.RegisterUserInfoServiceServer(server,&u)
	//启动服务端
	_ = server.Serve(listener)
}
```

## 例子2的客户端

```
package main
import "context"
func main()  {
	//连接
	conn,err := grpc.Dial("localhost:8080",grpc.WithInsecure())
	if err != nil {fmt.Println("无法连接服务端",err)}
	defer conn.Close()
	//实例化grpc,//go-micro没有,只有service.Client()
	client := proto.NewUserInfoServiceClient(conn)
	//定义一个request的参数
	req := proto.UserRequest{Name:"zs"}
	res,err := client.GetUserInfo(context.Background(),&req)
	if err != nil {fmt.Println("微服务失败!",err)}
	fmt.Println(res)
}
```

## 库gRPC的常量和变量

```
var ErrServerStopped = errors.New("grpc: the server has been stopped")
var _ http.Handler = (*Server)(nil)
const (
	defaultServerMaxReceiveMessageSize = 1024 * 1024 * 4
	defaultServerMaxSendMessageSize    = math.MaxInt32
)
var statusOK = status.New(codes.OK, "")
var logger = grpclog.Component("core")
var defaultServerOptions = serverOptions{
	maxReceiveMessageSize: defaultServerMaxReceiveMessageSize,
	maxSendMessageSize:    defaultServerMaxSendMessageSize,
	connectionTimeout:     120 * time.Second,
	writeBufferSize:       defaultWriteBufSize,
	readBufferSize:        defaultReadBufSize,
}

```

## 库gRPC的NewServer函数

```
func NewServer(opt ...ServerOption) *Server {
	opts := defaultServerOptions
	for _, o := range opt {
		o.apply(&opts)
	}
	s := &Server{
		lis:      make(map[net.Listener]bool),
		opts:     opts,
		conns:    make(map[transport.ServerTransport]bool),
		services: make(map[string]*serviceInfo),
		quit:     grpcsync.NewEvent(),
		done:     grpcsync.NewEvent(),
		czData:   new(channelzData),
	}
	chainUnaryServerInterceptors(s)
	chainStreamServerInterceptors(s)
	s.cv = sync.NewCond(&s.mu)
	if EnableTracing {
		_, file, line, _ := runtime.Caller(1)
		s.events = trace.NewEventLog("grpc.Server", fmt.Sprintf("%s:%d", file, line))
	}

	if s.opts.numServerWorkers > 0 {
		s.initServerWorkers()
	}

	if channelz.IsOn() {
		s.channelzID = channelz.RegisterServer(&channelzServer{s}, "")
	}
	return s
}
```

## 库gRPC的init函数

```
func init() {
   internal.GetServerCredentials = func(srv *Server) credentials.TransportCredentials {
      return srv.opts.creds
   }
}
```

## 库gRPC定义methodHandler

```
type methodHandler func(srv interface{}, ctx context.Context, dec func(interface{}) error, interceptor UnaryServerInterceptor) (interface{}, error)
```

## 库gRPC的结构MethodDesc

```
type MethodDesc struct {
   MethodName string
   Handler    methodHandler
}
```

## 库gRPC的结构ServiceDesc

```
type ServiceDesc struct {
   ServiceName string
   // The pointer to the service interface. Used to check whether the user
   // provided implementation satisfies the interface requirements.
   HandlerType interface{}
   Methods     []MethodDesc
   Streams     []StreamDesc
   Metadata    interface{}
}
```

## 库gRPC的结构serverWorkerData

```
type serverWorkerData struct {
   st     transport.ServerTransport
   wg     *sync.WaitGroup
   stream *transport.Stream
}
```

## 库gRPC的结构Server

```
type Server struct {
   opts serverOptions
   mu       sync.Mutex // guards following
   lis      map[net.Listener]bool
   conns    map[transport.ServerTransport]bool
   serve    bool
   drain    bool
   cv       *sync.Cond
   services map[string]*serviceInfo 
   events   trace.EventLog
   quit               *grpcsync.Event
   done               *grpcsync.Event
   channelzRemoveOnce sync.Once
   serveWG            sync.WaitGroup 
   channelzID int64 // channelz unique identification number
   czData     *channelzData
   serverWorkerChannels []chan *serverWorkerData
}
```

## 库gRPC的结构serverOptions

```
type serverOptions struct {
   creds                 credentials.TransportCredentials
   codec                 baseCodec
   cp                    Compressor
   dc                    Decompressor
   unaryInt              UnaryServerInterceptor
   streamInt             StreamServerInterceptor
   chainUnaryInts        []UnaryServerInterceptor
   chainStreamInts       []StreamServerInterceptor
   inTapHandle           tap.ServerInHandle
   statsHandler          stats.Handler
   maxConcurrentStreams  uint32
   maxReceiveMessageSize int
   maxSendMessageSize    int
   unknownStreamDesc     *StreamDesc
   keepaliveParams       keepalive.ServerParameters
   keepalivePolicy       keepalive.EnforcementPolicy
   initialWindowSize     int32
   initialConnWindowSize int32
   writeBufferSize       int
   readBufferSize        int
   connectionTimeout     time.Duration
   maxHeaderListSize     *uint32
   headerTableSize       *uint32
   numServerWorkers      uint32
}
```

## 库gRPC的结构ServiceInfo

```
type ServiceInfo struct {
   Methods []MethodInfo
   Metadata interface{}
}
```

## 库gRPC的结构serviceInfo

```
type serviceInfo struct {
   serviceImpl interface{}
   methods     map[string]*MethodDesc
   streams     map[string]*StreamDesc
   mdata       interface{}
}
```

## 库gRPC的结构ServerOption

```
type ServerOption interface {
   apply(*serverOptions)
}
```

## 库gRPC的结构funcServerOption

```
type funcServerOption struct {
   f func(*serverOptions)
}
func (fdo *funcServerOption) apply(do *serverOptions) {
	fdo.f(do)
}
```

## 库gRPC的结构EmptyServerOption

```
type EmptyServerOption struct{}
```

## 库gRPC的结构serverWorkerData

```
type serverWorkerData struct {
   st     transport.ServerTransport
   wg     *sync.WaitGroup
   stream *transport.Stream
}
```

## 库gRPC的结构listenSocket

```
type listenSocket struct {
   net.Listener
   channelzID int64
}
func (l *listenSocket) ChannelzMetric() *channelz.SocketInternalMetric {.}
func (l *listenSocket) Close() error {.}
```

## 库gRPC的结构MethodInfo

```
type MethodInfo struct {
   Name string
   IsClientStream bool
   IsServerStream bool
}
```

## 库gRPC的结构channelzServer

```
type channelzServer struct {
   s *Server
}
func (c *channelzServer) ChannelzMetric() *channelz.ServerInternalMetric {
	return c.s.channelzMetric()
}
```

## 库gRPC的接口ServiceRegistrar

```
type ServiceRegistrar interface {
   RegisterService(desc *ServiceDesc, impl interface{})
}
```

## 库gRPC的结构Server的方法

```
func (s *Server) printf(format string, a ...interface{}) {
   if s.events != nil {
      s.events.Printf(format, a...)
   }
}
func (s *Server) errorf(format string, a ...interface{}) {
	if s.events != nil {
		s.events.Errorf(format, a...)
	}
}
func (s *Server) RegisterService(sd *ServiceDesc, ss interface{}) {..}
func (s *Server)register(sd *ServiceDesc, ss interface{}) {}
func (s *Server) GetServiceInfo() map[string]ServiceInfo {}
func (s *Server) useTransportAuthenticator(rawConn net.Conn) (net.Conn, credentials.AuthInfo, error) {.}
func (s *Server) Serve(lis net.Listener) error {.}
func (s *Server) handleRawConn(rawConn net.Conn) {.}
func (s *Server) newHTTP2Transport(c net.Conn, authInfo credentials.AuthInfo) transport.ServerTransport {.}
func (s *Server)serveStreams(st transport.ServerTransport){}
func (s *Server) ServeHTTP(w http.ResponseWriter, r *http.Request) {.}
func (s *Server) traceInfo(st transport.ServerTransport, stream *transport.Stream) (trInfo *traceInfo) {.}
func (s *Server) addConn(st transport.ServerTransport)bool{}
func (s *Server)removeConn(st transport.ServerTransport){}
func(s *Server)channelzMetric() *channelz.ServerInternalMetric{.}
func (s *Server) incrCallsSucceeded() {
	atomic.AddInt64(&s.czData.callsSucceeded, 1)}
func (s *Server) incrCallsFailed() {
	atomic.AddInt64(&s.czData.callsFailed, 1)}
func (s *Server) sendResponse(t transport.ServerTransport, stream *transport.Stream, msg interface{}, cp Compressor, opts *transport.Options, comp encoding.Compressor) error {}
func (s *Server) handleStream(t transport.ServerTransport, stream *transport.Stream, trInfo *traceInfo) {}
func (s *Server) Stop() {}
func (s *Server) GracefulStop() {}
func (s *Server)getCodec(contentSubtype string) baseCodec{}
func (s *Server) initServerWorkers() {.}
func (s *Server) serverWorker(ch chan *serverWorkerData) {.}
func (s *Server) stopServerWorkers() {
	for i := uint32(0); i < s.opts.numServerWorkers; i++ {
		close(s.serverWorkerChannels[i])
	}
}
```

## 库gRPC的内部函数

```
func SetHeader(ctx context.Context, md metadata.MD) error {}
func SendHeader(ctx context.Context, md metadata.MD)error{}
func SetTrailer(ctx context.Context, md metadata.MD)error {}
func Method(ctx context.Context) (string, bool) {}
func newFuncServerOption(f func(*serverOptions)) *funcServerOption {
	return &funcServerOption{
		f: f,
	}
}
func WriteBufferSize(s int) ServerOption {
	return newFuncServerOption(func(o *serverOptions) {
		o.writeBufferSize = s
	})
}
func ReadBufferSize(s int) ServerOption {
	return newFuncServerOption(func(o *serverOptions) {
		o.readBufferSize = s
	})
}
func KeepaliveParams(kp keepalive.ServerParameters) ServerOption {.}
func KeepaliveEnforcementPolicy(kep keepalive.EnforcementPolicy) ServerOption {.}
```

## 库gRPC的函数InitialWindowSize

```
func InitialWindowSize(s int32) ServerOption {
	return newFuncServerOption(func(o *serverOptions) {
		o.initialWindowSize = s
	})
}
```

## 库gRPC的函数InitialConnWindowSize

```
func InitialConnWindowSize(s int32) ServerOption {
   return newFuncServerOption(func(o *serverOptions) {
      o.initialConnWindowSize = s
   })
}
```

## 库gRPC的函数CustomCodec

```
func CustomCodec(codec Codec) ServerOption {
   return newFuncServerOption(func(o *serverOptions) {
      o.codec = codec
   })
}
```

## 库gRPC的函数RPCCompressor

```
func RPCCompressor(cp Compressor) ServerOption {
   return newFuncServerOption(func(o *serverOptions) {
      o.cp = cp
   })
}
```

## 库gRPC的函数RPCDecompressor

```
func RPCDecompressor(dc Decompressor) ServerOption {
   return newFuncServerOption(func(o *serverOptions) {
      o.dc = dc
   })
}
```

## 库gRPC的函数MaxMsgSize

```
func MaxMsgSize(m int) ServerOption {
   return MaxRecvMsgSize(m)
}
```

## 库gRPC的函数MaxRecvMsgSize

```
func MaxRecvMsgSize(m int) ServerOption {
   return newFuncServerOption(func(o *serverOptions) {
      o.maxReceiveMessageSize = m
   })
}
```

## 库gRPC的函数MaxSendMsgSize

```
func MaxSendMsgSize(m int) ServerOption {
   return newFuncServerOption(func(o *serverOptions) {
      o.maxSendMessageSize = m
   })
}
```

库gRPC的函数

```
func MaxConcurrentStreams(n uint32) ServerOption {
   return newFuncServerOption(func(o *serverOptions) {
      o.maxConcurrentStreams = n
   })
}
```

库gRPC的函数

```
func Creds(c credentials.TransportCredentials) ServerOption {
   return newFuncServerOption(func(o *serverOptions) {
      o.creds = c
   })
}
```

库gRPC的函数

```
func UnaryInterceptor(i UnaryServerInterceptor) ServerOption {
   return newFuncServerOption(func(o *serverOptions) {
      if o.unaryInt != nil {
         panic("The unary server interceptor was already set and may not be reset.")
      }
      o.unaryInt = i
   })
}
```

库gRPC的函数

```
func ChainUnaryInterceptor(interceptors ...UnaryServerInterceptor) ServerOption {
   return newFuncServerOption(func(o *serverOptions) {
      o.chainUnaryInts = append(o.chainUnaryInts, interceptors...)
   })
}
```

库gRPC的Dial函数

```
func Dial(target string, opts ...DialOption) (*ClientConn, error) {
   return DialContext(context.Background(), target, opts...)
}
```

库gRPC的函数

```
func DialContext(ctx context.Context, target string, opts ...DialOption) (conn *ClientConn, err error) {.}
```



库gRPC的结构ClientConn

```
type ClientConn struct {
   ctx    context.Context
   cancel context.CancelFunc
   target       string
   parsedTarget resolver.Target
   authority    string
   dopts        dialOptions
   csMgr        *connectivityStateManager
   balancerBuildOpts balancer.BuildOptions
   blockingpicker    *pickerWrapper
   safeConfigSelector iresolver.SafeConfigSelector
   mu              sync.RWMutex
   resolverWrapper *ccResolverWrapper
   sc              *ServiceConfig
   conns           map[*addrConn]struct{}
   // Keepalive parameter can be updated if a GoAway is received.
   mkp             keepalive.ClientParameters
   curBalancerName string
   balancerWrapper *ccBalancerWrapper
   retryThrottler  atomic.Value
   firstResolveEvent *grpcsync.Event
   channelzID int64 // channelz unique identification number
   czData     *channelzData
   lceMu   sync.Mutex // protects lastConnectionError
   lastConnectionError error
}
```

库gRPC的函数

库gRPC的函数

库gRPC的函数

库gRPC的函数