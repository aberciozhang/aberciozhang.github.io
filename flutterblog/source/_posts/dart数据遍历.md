---
title: Dart数据遍历
tags:
  - forEach
  - where
categories: 语言
copyright: true
top: 1
toc: true
sidebar: true
date: 2021-02-08 17:55:43
---
摘要:
    Flutter快速开发App
    
<!-- more -->

### 遍历forEach

```
void main() {
  List myList = ['香蕉','苹果','西瓜'];
  for (int i =0;i<myList.length;i++){
    print(myList[i]);
  }
  for (var item in myList){
    print(item);
  }
  myList.forEach((value){print(value);});
  myList.forEach((value)=>print(value));//同上
  List numList = [1,3,4];
  var newList = numList.map((value){
    return value*2;
  }).toList();
  print(newList);//[2, 6, 8],如果没有toList()则显示(2, 6, 8)
  
}
```

### 遍历map

### 遍历where

```
void main() {
  List myList = [1,2,4,8,3,5,3,7,7];
  var m = myList.where((v){
    return v>5;
  });
  print(m);//(8, 7, 7)转换成List需要用toList(),则显示[8,7,7]
  var f = myList.any((value){
    return value>5;
  });
  print(f);//true  
  var p = myList.every((value){
    return value>5;
  });
  print(p);//false 
}
```

### 遍历any

### 遍历every