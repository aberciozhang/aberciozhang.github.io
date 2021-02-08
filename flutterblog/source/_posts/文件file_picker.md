---
title: 文件file_picker
tags:
  - file
  - null
categories: 文件
copyright: true
top: 1
toc: true
sidebar: true
date: 2021-02-08 16:54:44
---
摘要:
    Flutter快速开发App
    
<!-- more -->

## file_picker

g

##### 依赖

```
file_picker: ^2.1.5+1
```

导包

```
import 'package:file_picker/file_picker.dart';
```

##### Mobile, Desktop & Web

##### 参数Type

```
FileType.any
FileType.custom
FileType.image
FileType.video
FileType.media
FileType.audio
```

##### `方法pickFiles()`

```
// Will let you pick one file path, from all extensions
FilePickerResult result = await FilePicker.platform.pickFiles(type: FileType.any);

if(result != null) {
 File file = File(result.files.first.path);
}

// Will filter and only let you pick files with svg and pdf extension
FilePickerResult result = await FilePicker.platform.pickFiles(type: FileType.custom, allowedExtensions: ['svg', 'pdf']);

if(result != null) {
 File file = File(result.files.first.path);
}
```

##### `getDirectoryPath()`

```
ios,Requires iOS 11 or above
Android,Requires SDK 21 or above
Desktop,-
Web,Not supported
```

##### `clearTemporaryFiles()`

```
iOS,All picked files are cached, so this will immediately remove them.
Android,Since 2.0.0, all picked files are cached, so this will immediately remove them.
Desktop & Web,Not implemented as it won't have any effect. Paths are always referencing the original files.
```

##### **参数allowedExtensions**

```
类型 List<String>
Accepts a list of allowed extensions to be filtered. Eg. [.pdf, .jpg]
default:-
```

##### **参数allowCompression**

```
bool
默认true
Defines whether image and/or video files should be compressed automatically by OS when picked. On Android has no effect as it always returns the original or integral file copy.
```



##### **参数withData**

```
bool
默认:true on Web, false everywhere else
Sets if the file should be immediately loaded into memory and available as Uint8List on its PlatformFile instance.
```



##### **参数withReadStream**

```
bool
默认false
Allows the file to be read into a Stream<List<int>> instead of immediately loading it into memory, to prevent high usage, specially with bigger files.
```



##### **参数onFileLoading**

```
调用函数Function(FilePickerStatus)
When provided, will receive the processing status of picked files. This is particularly useful if you want to display a loading dialog or so when files are being downloaded/cached
```

##### 例子Single file

```
FilePickerResult result = await FilePicker.platform.pickFiles();

if(result != null) {
   File file = File(result.files.single.path);
} else {
   // User canceled the picker
}
```

##### 例子Multiple files

```
FilePickerResult result = await FilePicker.platform.pickFiles(allowMultiple: true);

if(result != null) {
   List<File> files = result.paths.map((path) => File(path)).toList();
} else {
   // User canceled the picker
}
```

##### Multiple files with extension filter

```
FilePickerResult result = await FilePicker.platform.pickFiles(
          type: FileType.custom,
          allowedExtensions: ['jpg', 'pdf', 'doc'],
        );
```



##### Load result and file details 

```
FilePickerResult result = await FilePicker.platform.pickFiles();

if(result != null) {
   PlatformFile file = result.files.first;
   
   print(file.name);
   print(file.bytes);
   print(file.size);
   print(file.extension);
   print(file.path);
} else {
   // User canceled the picker
}
```

