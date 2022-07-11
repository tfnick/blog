---
title: '记一次Unexpected end of file from server异常排查经历'
description: '记一次Unexpected end of file from server异常排查经历，过程很糟心'
summary: "unexpected end of file from server排查"
date: '2022-05-28'
author: '飞天'
usePageBundles: true
---


## 问题描述

公司的某个服务A通过统一的正向代理访问外部的第三方接口，数据流（服务A -> 正向代理 -> 外部第三方服务），生产环境会间歇性的出现unexpected end of file from server异常。

### 排查步骤一

初步怀疑是代码写法问题，于是将如下写法

```java
Proxy proxy = new Proxy(Proxy.Type.HTTP, new InetSocketAddress("<PROXY.HOST>", 80)));
HttpsURLConnection con = (HttpsURLConnection) url.openConnection(proxy);
```

改为基于restTemplate

```java
@Bean
public RestTemplate restTemplate() {
    SimpleClientHttpRequestFactory requestFactory = new SimpleClientHttpRequestFactory();

    Proxy proxy = new Proxy(Type.HTTP, new InetSocketAddress("<PROXY.HOST>", 80));
    requestFactory.setProxy(proxy);

    return new RestTemplate(requestFactory);
}
```

但是实际上，resttemplate底层同样是基于`HttpsURLConnection`，所以问题并没有解决。

### 排查步骤二

此异常一般说明，服务端关闭了连接，从而客户端获取response时会抛出此异常。我们认为异常可能是外部第三方关闭了连接或者代理关闭了连接，因此我们期望在代理上抓包排查异常原因，但是生产环境不好操作。作罢！


### 排查步骤三

正路不通，我们联系第三方，第三方反馈没有我方发生异常时的请求记录；我们找到代理上的其他服务B，咨询是否有类似的异常抛出，其他服务B反馈没有遇到过。 我们一脸问号？？？

综合上述信息，我们基本排除了（服务A -> 正向代理 -> 外部第三方服务）三个节点中出问题的可能性（不能说100%排除，只能说概率90%以上）。那么只能从http协议本身入手了，我们知道http协议有长短连接的区别，长连接在生命周期结束的场景下会被服务端自动关闭。

我们的请求默认情况下就是长连接。那么有没有可能是加入代理节点后，这个关闭动作因为“延迟”，被客户端感知到了呢 ？

验证非常好验证，只需要指定非长连接后，观察异常是否还出现即可：

即将http 请求 header `Connection: Keep-Alive` 改为 `Connection: Close`

上线后，异常不再出现... 

收工!~~~