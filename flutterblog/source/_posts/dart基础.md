---
title: dart基础
tags:
  - dart
  - null
categories: 语言
copyright: true
top: 1
toc: true
sidebar: true
date: 2021-02-08 16:54:44
---
摘要:
    Flutter快速开发App
    
<!-- more -->

### 算数运算符

```
+加
_减
*乘
/除,得数带小数
~/除取整
%除取余
++自加b=++a区别b=a++,前面是先加后赋值,后面是先赋值后自加
__自减
```

### 比较运算符

```
==判断后得出bool值
!=
>
<
>=
<=
```

### 类型运算符

```
as类型转成
is判断类型返回bool值
is!判断不是该类型返回bool值
```

### 复合运算符

```
+=
-=
/=
*=
~/=
%=
<<=复合左移运算符
>>=复合右移运算符
&=复合按位与运算符
^=复合按位异或运算符
|=复合按位或运算符
```

### 逻辑运算符

```
&&
||
!
```

### 位运算符

```
&
|
~按位非运算
^按位异或运算
```

### 条件运算符

```
条件?选项1:选项2 三目运算
??空条件运算 例 n??=3如n为空则赋值3,如不为空则不赋值保持原值
?空点运算符 例 person?.age 是如果person的age不为空则操作
```

### 级联运算符

```
void main() {
  var p1 = Person();
  p1.name = "zhang";
  p1.age = 15;
  print(p1.name);
  var p2 = Person()..name="chun"..age=19;
  print(p2.age);
  
}
class Person{
  String name;
  int age;
}
```

### 点运算符

```
.类的方法和属性
```

### 条件分支语句

```
void main() {
  var flag = true;
  //条件分支1
  if(flag){print("条件为真");}
  //条件分支2
  if(!flag){
    print("取反后为真");
  }else{
    print("取反后为false");
  }
  //条件分支3
  int n = 100;
  if(n<60){
    print("不及格!");
  }else if(n<85){
    print("良好.");
  }else if(n<100){
    print("优秀!");
  }else{
    print("100");
  }  
}
```

### 循环语句

```
void main() {
  var n = 1;
  var s = 0;
  //while循环
  while(n<=100){
    s += n;
    n++;
  }
  print(s);
  n = 1;
  s = 0;
  //do..while循环
  do{
    s += n;
    n++;
  }while(n<=100);
  print(s);
  s = 0;
  //for循环
  for(var i=1;i<=100;i++){
    s += i;
  }
  print(s);
  var list1 = [1,2,3,4];
  //for..in循环
  for(var num in list1){
    print(num);
  }
}
```

### 分支选择语句

```

```

### 中断语句

```
//break跳出while或for循环或switch循环
//continue

```

### 异常处理

### main函数

```
void main(List<String> argus) {
print(argus);//显示[]
}
```

### 自定义函数

```
void main(List<String> argus) {
  var res = addFunc(3,6);
  print(res);
}
num addFunc(num a,num b){
  return a+b;
}
```

### 参数变量必写函数

```
void main() {
  myFunc(name:"zhang");
}
//{}里参数变量名必须写 name:"zhang"
myFunc({String name,int age}){  
  if(name!=null){
    print("名字是$name");
  }
  if(age!=null){
    print("年龄是$age");
  }
}
```

### 可选参数的函数

```
void main() {
  myFunc("zhang");
}
//[]里参数可选,外面的参数必写
myFunc(String name,[int age]){  //可选
  if(name!=null){
    print("名字是$name");
  }
  if(age!=null){
    print("年龄是$age");
  }
}
```

### 默认参数的函数

```
void main() {
  myFunc1(age:18);
  myFunc2();
  myFunc2("hua",200);
  
}
myFunc1({String name="默认名",int age=100}){  
  if(name!=null){
    print("名字是$name");
  }
  if(age!=null){
    print("年龄是$age");
  }
}
myFunc2([String name="默认名",int age=100]){  
  if(name!=null){
    print("myFunc2名字是$name");
  }
  if(age!=null){
    print("myFunc2年龄是$age");
  }
}
```

### 匿名函数

```
void main() {
  var func = (a,b){
  return a+b;
  };
  var res = func(1,5);
  print(res);
}
```

### 自执行函数

```
//((int n){print(n)})(12);
void main() {
  (name){
    print("hello $name!");
  }("zhang");
}
```

### 箭头函数

```
void main(List<String> argus) {
  var res = addFunc(3,6);
  print(res);
}
num addFunc(num a,num b)=>(a+b);
```

### 闭包函数

```
void main() {
  var close = func("zhang");
  print(close());//hello zhang
}
func(name){
  return ()=>"hello $name";
}
```

```
void main() {
  var b = fn();
  b();
  b();
  b();
}
fn(){
  var a =123;//不会污染全局,常驻内存
  return (){
    a++;
    print(a);
  };
}
```

### 递归函数

```
void main() {
  var sum = 1;
  fn(int n){
    sum *= n;
    if(n==1){return;}
    fn(n-1);
  }  
 fn(9);
 print(sum);
 print(factorial(6));//720
}
int factorial(int n) {
  var s = 1;
  if (n > 1) {
    s = n * factorial(n - 1);
  }
  return s;
}
```

### 语言Dart注释

```
//注释单行
///注释单行,换行自动添加注释
/*
注释多行
*/
```

## 