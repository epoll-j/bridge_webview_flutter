# Add JsBridge to the WebView

[A Flutter plugin that provides a JsBridge on WebView widget.](https://pub.dev/packages/bridge_webview_flutter)

On iOS the WebView widget is backed by a [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview);
On Android the WebView widget is backed by a [WebView](https://developer.android.com/reference/android/webkit/WebView).

This plugin based on the [webview_flutter](https://pub.dev/packages/webview_flutter)

## Developers Preview Status

To use this plugin on iOS you need to opt-in for the embedded views preview by
adding a boolean property to the app's `Info.plist` file, with the key `io.flutter.embedded_views_preview`
and the value `YES`.

## Keyboard support - not ready for production use
Keyboard support within webviews is experimental. The Android version relies on some low-level knobs that have not been well tested
on a broad spectrum of devices yet, and therefore **it is not recommended to rely on webview keyboard in production apps yet**.
See the [webview-keyboard](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22p%3A+webview-keyboard%22) for known issues with keyboard input.

## Setup

### iOS
Opt-in to the embedded views preview by adding a boolean property to the app's `Info.plist` file
with the key `io.flutter.embedded_views_preview` and the value `YES`.

## Usage
Add `bridge_webview_flutter` as a [dependency in your pubspec.yaml file](https://flutter.io/platform-plugins/).

### Register a Flutter handler function so that js can call
```
onWebViewCreated: (WebViewController webViewController) {
    _controller.complete(webViewController);
    webViewController.registerHandler("methodName", response: "r1", onCallBack: (callBackData) {
        print(callBackData.name); // handler name
        print(callBackData.data); // callback data ({'param': '1'})
    });
}
```
#### js can call this handler method "methodName" through:
```
WebViewJavascriptBridge.callHandler(
    'methodName'
    , {'param': '1'}
    , function(data) {
        // data is r1
    }
);
```

### Register a JavaScript handler function so that flutter can call
```
WebViewJavascriptBridge.registerHandler("methodName", function(data, responseCallback) {
    // data is 'sendData'
    responseCallback('r2');
});
```
#### flutter can call this js handler function "methodName" through:
```
onWebViewCreated: (WebViewController webViewController) {
    _controller.complete(webViewController);
    webViewController.callHandler("methodName", data: "sendData", onCallBack: (callBackData) {
        print(callBackData.name); // handler name
        print(callBackData.data); // callback data (r2)
    });
}
```