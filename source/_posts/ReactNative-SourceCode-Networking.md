title: ReactNative SourceCode Networking
date: 2016-07-13 15:03:36
author: [mac.meng]
tags: [ReactNative,Networking]
---
React Native 源码阅读(1):网络

### fetch in RN 0.25 调用链
* app/AppNetworking.js

```
fetch(reqUrl,fetchObj)
```
	
* node_modules/react-native/Libraries/Fetch/fetch.js line 377: 

```
line 368: self.fetch = function(input, init) {
var xhr = new XMLHttpRequest()
```

* node_modules/react-native/Libraries/JavaScriptAppEngine/Initialzation/InitializeJavaScriptAppEngine.js  line 137:
定义的全局fetch和XMLHttpRequest 以及其他的

```
function setUpXHR() {
  // The native XMLHttpRequest in Chrome dev tools is CORS aware and won't
  // let you fetch anything from the internet
  polyfillGlobal('XMLHttpRequest', require('XMLHttpRequest'));
  polyfillGlobal('FormData', require('FormData'));

  var fetchPolyfill = require('fetch');
  polyfillGlobal('fetch', fetchPolyfill.fetch);
  polyfillGlobal('Headers', fetchPolyfill.Headers);
  polyfillGlobal('Request', fetchPolyfill.Request);
  polyfillGlobal('Response', fetchPolyfill.Response);
}
```




* node_modules/react-native/Libraries/Network/XMLHttpRequest.ios.js line 15:

```	
var RCTNetworking = require('RCTNetworking');
```
* node_modules/react-native/Libraries/Network/RCTNetworking.ios.js line 13:

```
var RCTNetworkingNative = require('NativeModules').Networking;
```

* node_modules/react-native/Libraries/Network/RCTNetworking.m line 135 and 432:

```
RCT_EXPORT_MODULE()
RCT_EXPORT_METHOD(sendRequest:(NSDictionary *)query
                  responseSender:(RCTResponseSenderBlock)responseSender)
```

* 分析iOS的实现, 使用了系统原生的 NSURLRequest,NSURLSession等
* 分析Android的实现. 使用了OKHttp

