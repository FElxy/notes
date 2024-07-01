自定义的flutter webview中是没有开启请求地理位置权限弹窗的，这里需要单独设置一下

设置方法
```dart
setGeolocationPermissionsPromptCallbacks(
    onShowPrompt: (request) async {
    // request location permission
    final locationPermissionStatus =
        await Permission.locationWhenInUse.request();

    // return the response
    return GeolocationPermissionsResponse(
    allow: locationPermissionStatus == PermissionStatus.granted,
    retain: false,
    );
})
```

关键代码
```dart
WebViewController webViewController =
    WebViewController.fromPlatformCreationParams(params,
        onPermissionRequest: (request) {
    LogUtil().i('androidOnPermissionRequest: $request');
    request.grant();
})
        ..setJavaScriptMode(JavaScriptMode.unrestricted)
        ..setBackgroundColor(Colors.transparent);


// ios开发环境支持safari真机调试
if (webViewController.platform is WebKitWebViewController) {
    if (!const bool.fromEnvironment('dart.vm.product')) {
    (webViewController.platform as WebKitWebViewController)
        .setInspectable(true);
    }
}

// 如果是android情况下，setMediaPlaybackRequiresUserGesture
if (webViewController.platform is AndroidWebViewController) {
    if (!const bool.fromEnvironment('dart.vm.product')) {
    AndroidWebViewController.enableDebugging(true);
    }
    (webViewController.platform as AndroidWebViewController)
    ..setTextZoom(100)
    ..setMediaPlaybackRequiresUserGesture(false)
    ..setGeolocationPermissionsPromptCallbacks(
        onShowPrompt: (request) async {
        // request location permission
        final locationPermissionStatus =
            await Permission.locationWhenInUse.request();

        // return the response
        return GeolocationPermissionsResponse(
        allow: locationPermissionStatus == PermissionStatus.granted,
        retain: false,
        );
    });
}
```