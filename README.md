## Features

Upload to the aliyun_oss . It can be a video, a picture, or something else

## Getting started

### Install
```
aliyunoss_plus_flutter: ^1.0.0
```

## Usage

```dart
import 'package:aliyunoss_plus_flutter/aliyunoss_plus_flutter.dart';
import 'package:flutter/material.dart';

class AliyunPage extends StatefulWidget {
  const AliyunPage({super.key});

  @override
  State<AliyunPage> createState() => _AliyunPageState();
}

class _AliyunPageState extends State<AliyunPage> {
  String filePath = "/Users/xxx/Downloads/video.mp4";
  late AliyunOssClient client;
  late AliyunOssConfig config;

  final String uploadId1 = "test-1";
  final String uploadId2 = "test-2";
  String showText = "准备上传";
  @override
  void initState() {
    client = AliyunOssClient(
        accessKeyId: "STS.NSkZtxxxxxxxE",
        accessKeySecret: "77XxxxxXXXXXXXXXXXXXXXXXXXXXXtFv",
        securityToken:
            "CAXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX==");
    config = AliyunOssConfig(
        endpoint: "https://oss-cn-shanghai.aliyuncs.com",
        bucket: "pvideo-xxx",
        directory: "dev-nom/20221103/");

    AliyunOssClient.eventStream.listen((event) {
      print("eventStream:${event.toString()}");
      if (mounted) {
        setState(() {
          showText = event.toString();
        });
      }
    });
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        appBar: AppBar(
          title: const Text('AliyunPage'),
        ),
        body: Column(
          crossAxisAlignment: CrossAxisAlignment.center,
          children: <Widget>[
            Text(
              'filePath: $filePath',
            ),
            Row(mainAxisAlignment: MainAxisAlignment.spaceAround, children: [
              ElevatedButton(
                onPressed: () async {
                  final res = await client.upload(
                      id: uploadId1, config: config, filePath: filePath);
                  print("result:${res.toString()}");
                },
                child: const Text('直接上传'),
              ),
              ElevatedButton(
                onPressed: () async {
                  client.uploadMultipart(
                      id: uploadId2, config: config, filePath: filePath);
                },
                child: const Text('分片上传'),
              ),
              ElevatedButton(
                onPressed: () async {
                  AliyunOssClient.cancelTask(uploadId2);
                },
                child: const Text('暂停上传'),
              ),
              ElevatedButton(
                onPressed: () async {
                  client.resumeUpload(uploadId2);
                },
                child: const Text('恢复上传'),
              ),
            ]),
            Text(showText),
          ],
        ));
  }
}

```


