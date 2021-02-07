---
title: 微服务go-micro
date: 2021-02-05 14:46:57
tags:
    - go-micro
categories: 微服务
top: 90
toc: true # 是否启用内容索引
sidebar: true # 是否启用sidebar侧边栏，none：不启用
---
go语言最常用的微服务框架
微服务模块化使用
<!-- more -->
## 微服务Go-Micro

## 插件地址

```
https://github.com/asim/go-micro/releases
https://github.com/micro/go-plugins
https://github.com/micro/go-micro
go build -o service main.go plugins.go
go get -u github.com/micro/micro
go get -u github.com/micro/go-micro
go get -u github.com/micro/protoc-gen-micro
```

## 可能出现的版本问题

```
//在go.mod中最后添加
replace google.golang.org/grpc => google.golang.org/grpc v1.26.0
replace github.com/lucas-clemente/quic-go => github.com/lucas-clemente/quic-go v0.14.1

```

## 例子1的protobuf文件

```
//protoc --go_out=plugins=micro:. message.proto无法使用
//下面自动生成2个go文件都是同一个pkg,没有
//protoc --go_out=:. --micro_out=:. message.proto
syntax = "proto3";
package message;
message Student{
    string name = 1;
    string classes = 3;
    int32 grade = 4;
}
message StudentRequest{
    string name = 1;
}
service StudentService{
    rpc GetStudent (StudentRequest) returns (Student);
}
```

## 例子1的服务端

```
package main
type StudentManager struct{}
func (sm *StudentManager) GetStudent(ctx context.Context, request *message.StudentRequest, response *message.Student) error {
	studentMap := map[string]message.Student{
		"davie":  {Name: "davie", Classes: "软件工程", Grade: 80},
		"steven": {Name: "steven", Classes: "计算机科学", Grade: 90},
		"tony":   {Name: "tony", Classes: "网络工程", Grade: 85},
		"jack":   {Name: "jack", Classes: "工商管理", Grade: 96},
	}
	if request.Name == "" {
		return errors.New("请求参数错误!")
	}
	student := studentMap[request.Name]
	if student.Name != "" {
		fmt.Println(student)
		*response = student
		return nil
	}
	return errors.New("查无此人")
}
func main() {
	//创建服务实例
	service := micro.NewService(micro.Name("student_service"), micro.Version("v1.0.0"), )
	//服务初始化
	service.Init()
	//注册
	_ = message.RegisterStudentServiceHandler(service.Server(), new(StudentManager))
	//运行
	err := service.Run()
	if err != nil {
		log.Fatal(err)
	}
}
```

## 例子1的客户端

```
package main
func main() {
	service := micro.NewService(micro.Name("student.client"))
	service.Init()
	//grpc有services.NewProdServiceClient(conn)
	//go-micro没有,只有service.Client()
	studentService := message.NewStudentService("student_service",service.Client())
	res,err := studentService.GetStudent(context.TODO(),&message.StudentRequest{Name:"tony"})
	if err!= nil{fmt.Println("错误提示",err)}
	fmt.Println(res)
}
```

例子2的

```
//proto>protoc -I . --micro_out=. --go_out=. ./hello.proto
syntax = "proto3";
message InfoRequest{
    string username = 1;
}
message InfoResponse{
    string msg = 2;
}
service Hello{
    rpc Info(InfoRequest) returns(InfoResponse);
}
```

例子2的服务端

```
package main
type Hello struct {}
func (h *Hello)Info(ctx context.Context, req *hello.InfoRequest,res *hello.InfoResponse) error{
	res.Msg = "你好" + req.Username
	return nil}
func main()  {//上面可以封装
	//得到服务端实例
	service := micro.NewService(micro.Name("hello_service"))
	//初始化
	service.Init()
	//服务注册hello.RegisterHelloHandler(service.Server(),new(Hello))
	err = service.Run()
	if err != nil {log.Fatal("服务注册失败!",err)}}
```

例子2的客户端

```
package main
func main() {
//得到客户端实例
	service := micro.NewService(micro.Name("hello_client"))
//初始化
	service.Init()
//得到服务函数
	helloService := hello.NewHelloService("hello_service", service.Client())
	res,_ := helloService.Info(context.TODO(),&hello.InfoRequest{Username:"q"})
	fmt.Println(res)
}
```

