flutter image 组件中的copyCrop方法可以对图片进行裁剪，但是太慢了

这里使用了method channel来调用原生方法进行图片裁剪


在dart文件中创建一个图片裁剪类，主要用来调用method channel
```dart
class ImageCropper {
  static const MethodChannel _channel = MethodChannel('channel');

  // 方法调用接口，传入图片数据和裁剪参数
  static Future<Uint8List> cropImage(
      Uint8List imageData, int x, int y, int width, int height) async {
    final Map<String, dynamic> args = {
      'imageData': imageData,
      'x': x,
      'y': y,
      'width': width,
      'height': height,
    };
    final Uint8List croppedData =
        await _channel.invokeMethod('cropImage', args);
    return croppedData;
  }
}

```

iOS部分的处理
```swift
iosChannel = FlutterMethodChannel(name: "channel", binaryMessenger: controller)
iosChannel?.setMethodCallHandler { (call: FlutterMethodCall, result: FlutterResult) in
    if call.method == "cropImage" {
        if let args = call.arguments as? [String: Any],
        let imageData = args["imageData"] as? FlutterStandardTypedData,
        let x = args["x"] as? Int,
        let y = args["y"] as? Int,
        let width = args["width"] as? Int,
        let height = args["height"] as? Int {
            
            // 尝试将字节数据转换为 UIImage
            guard let image = UIImage(data: imageData.data) else {
                result(FlutterError(code: "INVALID_IMAGE", message: "Cannot convert data to UIImage", details: nil))
                return
            }
            
            // 定义裁剪区域
            let cropRect = CGRect(x: x, y: y, width: width, height: height)
            
            // 尝试裁剪图像
            guard let cgImage = image.cgImage?.cropping(to: cropRect) else {
                result(FlutterError(code: "CROP_FAILED", message: "Failed to crop image", details: nil))
                return
            }
            
            // 创建裁剪后的 UIImage
            let croppedImage = UIImage(cgImage: cgImage)
            
            // 返回裁剪后的图像数据，JPEG 格式，压缩质量为 100%
            result(croppedImage.jpegData(compressionQuality: 1.0) ?? Data())
        } else {
            // 处理缺少或无效参数的情况
            result(FlutterError(code: "INVALID_ARGUMENTS", message: "Invalid arguments provided", details: nil))
        }
    }else {
        result(FlutterMethodNotImplemented)
    }
}

```

安卓部分的处理
```java
methodChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, "channel")
methodChannel?.setMethodCallHandler { call, result ->
    run {
        if (call.method.equals("cropImage")) {
            val data = call.argument<ByteArray>("imageData")
            val x = call.argument<Int>("x")
            val y = call.argument<Int>("y")
            val width = call.argument<Int>("width")
            val height = call.argument<Int>("height")

            if (data == null) {
                return null
            }
            val bitmap = BitmapFactory.decodeByteArray(data, 0, data.size)
            val croppedBitmap = Bitmap.createBitmap(bitmap, x ?: 0, y ?: 0, width ?: bitmap.width, height ?: bitmap.height)
            val stream = ByteArrayOutputStream()
            croppedBitmap.compress(Bitmap.CompressFormat.JPEG, 100, stream)
            var croppedData = stream.toByteArray()
            result.success(croppedData)
        }
    }
}

```

