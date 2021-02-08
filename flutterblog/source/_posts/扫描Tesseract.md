---
title: 扫描Tesseract
tags:
  - ocr
  - Tesseract
categories: 扫描
copyright: true
top: 1
toc: true
sidebar: true
date: 2021-02-08 16:54:44
---
摘要:
    Flutter快速开发App
    
<!-- more -->

## Tesseract OCR

##### 依赖

```
tesseract_ocr: ^0.3.1
```

##### 导包

```
import 'package:tesseract_ocr/tesseract_ocr.dart';
```

##### 相关依赖和包

```
tesseract_ocr: ^0.3.1
file_picker: ^2.1.5+1
flutter_spinkit: ^4.1.2+1
import 'package:file_picker/file_picker.dart';
import 'package:flutter_spinkit/flutter_spinkit.dart';
import 'package:tesseract_ocr/tesseract_ocr.dart';
```

##### assets目录

```
//该目录创建在项目根目录下
//assets目录里创建tessdata内含eng.traineddata文件
//assset目录里创建文件tessdata_config.json
assets:
    - images/
    - assets/
    - assets/tessdata/
```

##### image目录

```
//该目录创建也在项目根目录下
//image目录包含需要扫描的文字图片
```

##### tessdata_config

```
//配置文件
{
  "files": [
    "eng.traineddata",
    "OCRB.traineddata",
    "pdf.ttf",
    "financial.traineddata"
  ]
}
```

##### 使用方式

```
String text = await TesseractOcr.extractText('/path/to/image', language: 'eng');
```

##### 主要文件

```
import 'package:file_picker/file_picker.dart';
import 'package:flutter/material.dart';
import 'package:flutter_spinkit/flutter_spinkit.dart';
import 'package:tesseract_ocr/tesseract_ocr.dart';
void main() => runApp(MyApp());
class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => _MyAppState();
}
class _MyAppState extends State<MyApp> {
  bool _scanning = false;
  String _extractText = '';
  int _scanTime = 0;
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
          appBar: AppBar(
            title: const Text('Tesseract OCR'),
          ),
          body: Container(
            padding: EdgeInsets.all(16),
            child: ListView(
              children: <Widget>[
                Row(
                  mainAxisAlignment: MainAxisAlignment.spaceEvenly,
                  children: [
                    RaisedButton(
                      child: Text('Select image'),
                      onPressed: () async {
                        var file =
                            await FilePicker.getFilePath(type: FileType.image);
                        _scanning = true;
                        setState(() {});

                        var watch = Stopwatch()..start();
                        _extractText = await TesseractOcr.extractText(file);
                        _scanTime = watch.elapsedMilliseconds;

                        _scanning = false;
                        setState(() {});
                      },
                    ),
                    // It doesn't spin, because scanning hangs thread for now
                    _scanning
                        ? SpinKitCircle(
                            color: Colors.black,
                          )
                        : Icon(Icons.done),
                  ],
                ),
                SizedBox(
                  height: 16,
                ),
                Text(
                  'Scanning took $_scanTime ms',
                  style: TextStyle(color: Colors.red),
                ),
                SizedBox(
                  height: 16,
                ),
                Center(child: SelectableText(_extractText)),
              ],
            ),
          )),
    );
  }
}
```

