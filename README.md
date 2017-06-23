# SimpleJsBridge

> A simple and powerful library that implements the interaction between Android and javascript.

[TOC]

## 前言

之前写了一个关于Android和JS的简单交互的文章，[Android native和js简单交互](http://nelsonblog.me/2017/02/28/Android-native%E5%92%8Cjs%E7%AE%80%E5%8D%95%E4%BA%A4%E4%BA%92/)，里面的交互逻辑很简单。但有些展示性强的H5页面需要和Android native做一些复杂的交互，例如：js调用Android需要回调数据，之前的就不能满足了，所以需要实现一个可以通用双向交互的方案，本文就从2个角度来实现。

## 实现方式

### 方案一

在WebView中，如果java调用js的方法，是很容易的，使用webView.loadUrl("JavaScript:function()")即可，这样就做到了native调用h5层的单向通信，但是h5如何调用native层呢，我们需要去寻找一个通道。WebView有个方法`setWebChromeClient()`,可以设置WebChromeClient对象，这个对象有三个方法，`onJsAlert`，`onJsConfirm`，`onJsPrompt`，这三个方法的区别可以看w3c [JavaScript 消息框](http://www.w3school.com.cn/js/js_popup.asp)。
alert弹框在js中使用很频繁，我们不适合占用这个通道，confirm和prompt使用的频率相对alert来说，低一点。其实confirm的使用频率也不低，比如你点击网页上一个下载链接，会弹出来一个对话框提示下载和取消。这中场景还是很多的，因此也不能占用confirm通道。而prompt则不一样，Android很少使用到它，即使使用到了也会自定义的，该方法是弹出一个输入框，让你输入，输入完成后返回输入框中的内容。所以，占用prompt是再完美不过了。系统API方法如下：

```java
 @Override
    public boolean onJsAlert(WebView view, String url, String message, JsResult result) {
        return super.onJsAlert(view, url, message, result);
    }

    @Override
    public boolean onJsConfirm(WebView view, String url, String message, JsResult result) {
        return super.onJsConfirm(view, url, message, result);
    }

    @Override
    public boolean onJsPrompt(WebView view, String url, String message, String defaultValue, JsPromptResult result) {
        return false;
    }
```

### 方案二

## 感谢

[ Android JSBridge的原理与实现](http://blog.csdn.net/sbsujjbcy/article/details/50752595)
