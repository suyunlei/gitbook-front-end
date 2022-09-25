# 🤖 网络协议

## HTTP

### HTTP基本概念

HTTP，超文本传输协议，属于应用层上的一种面向对象的网络协议。 **注意**

1. HTTP是**无状态**的，即没有记忆能力。
2. HTTP 1.0是**无连接**的，一次请求就会断开连接。

### HTTP请求方法

* GET 获取资源
* POST 传输资源
* DELETE 删除资源
* PUT 更新资源
* HEAD 获取报文首部信息 - 可以测试链接的有效性

#### GET 和 POST 的区别

1. **浏览器的回退表现不同**。POST回退时会再次发出请求
2. GET请求地址会被浏览器主动**缓存**，POST不会
3. GET请求参数会添加到**URL**中，POST则在**Request Body**中
4. 因为URL**长度限制**，请求参数的大小不同。GET会有限制。
5. GET是**只读**的。

### HTTP 1.1

1. **持久连接**，TCP链接默认不关闭，需要手动关闭
2. **管道机制**，即在同一个TCP链接内，可以发送多个请求。 管道机制则是允许浏览器同时发出A请求和B请求，但是服务器还是按照顺序，先回应A请求，完成后再回应B请求。

#### HTTP1.1的缺陷

> 队头阻塞高延迟，无状态阻交互，明文传输不安全，服务推送不支持

1. 队头阻塞 (Head-of-line blocking)，即一个高延迟的请求会阻碍后续请求的进行
2. 无状态。纯净的HTTP请求是没有Cookie的
3. HTTP是明文传输。
4. 不支持服务器端的推送。

### HTTP 2

是基于Chrome的**SPDY**的，最大特性是客户端与服务器只建立**一个连接**

#### HTTP2的特性

1. 二进制传输
2. Header压缩
3. 服务器推送
4. 多路复用
5. 提高安全性
6. 解决了大文件的阻塞，把大文件分包发送

***

## WebSocket

WebSocket也是应用层协议，它最大的特点是，服务器可以主动向客户端推送信息，客户端也可以主动向服务器发送信息，双向通话。 例子： `ws://example.com:80/some/path`

### WebSocket的特点

1. 与 HTTP 协议有着良好的兼容性。默认端口也是80和443，并且握手阶段采用 HTTP 协议
2. 也是基于**TCP**基础上的
3. **没有同源限制**
4. 协议标识符是**ws**

### WebSocket api

可以向服务器发送消息并接受**基于事件驱动的响应**(即`addEventListener`)，这样就不用向服务器轮询获取数据了。

#### WebSocket构造函数

创建一个ws对象

```javascript
var ws = new WebSocket('ws://localhost:8080');
```

#### WebSocket.readyState

```javascript
switch (ws.readyState) {
  case WebSocket.CONNECTING: // value = 0
    // do something
    break;
  case WebSocket.OPEN: // value = 1
    // do something
    break;
  case WebSocket.CLOSING: // value = 2
    // do something
    break;
  case WebSocket.CLOSED: // value = 3
    // do something
    break;
  default:
    // this never happens
    break;
}
```

#### WebSocket.onopen

用于指定ws对象建立连接后的回调函数

```javascript
ws.onopen = function () {
  ws.send('Hello Server!'); // 发送数据
}
```

也可以利用事件监听实现,实现多个回调

```javascript
ws.addEventListener('open', function (event) {
  ws.send('Hello Server!');
});
```

#### WebSocket.onclose

指定连接关闭后的回调函数

```javascript
ws.onclose = function(event) {
  var code = event.code;
  var reason = event.reason;
  var wasClean = event.wasClean;
  // handle close event
};
```

也可以利用事件监听实现

```javascript
ws.addEventListener("close", function(event) {
  var code = event.code;
  var reason = event.reason;
  var wasClean = event.wasClean;
  // handle close event
});
```

#### WebSocket.onmessage

用于指定收到服务器返回数据的回调函数，处理响应

```javascript
ws.onmessage = function(event) {
  var data = event.data;
  // 处理数据
};
```

也可以利用事件监听实现：

```javascript
ws.addEventListener("message", function(event) {
  var data = event.data;
  // 处理数据
});
```

#### WebSocket.send()

向服务器发送数据

```javascript
ws.send('Hello');
```

## HTTPS

HTTPS = HTTP + TLS(SSL已经过时) port: 443

**过程**

* 先非对称加密

1. 客户端发送请求，表明自己支持的TLS版本和加密套件
2. 服务端发送请求，表明自己支持的TLS版本和加密套件
3. 服务端发送自己的证书
4. 服务端发送公钥
5. 客户端通过公钥加密后得到预主密钥，发送给服务端
6. 服务端收到预主密钥后，用私钥解密，得到会话密钥
7. 客户端通过预主密钥也计算出会话密钥

* 再对称加密

1. 服务端通知客户端加密的握手信息
2. 客户端和服务端通过会话密钥进行通信

## TCP/IP 网络模型

* 链路层 负责封装和解封装IP报文
* 网络层 负责寻找路由
* 传输层 负责报文的分组和重组，用TCP, UDP协议格式封装报文
* 应用层 负责向用户提供应用程序 HTTP, FTP, DNS

## TCP/IP 三次握手四次挥手

### 三次握手

1. 客户端发送syn到服务器，进入syn\_sent状态，等待服务器确认
2. 服务器收到syn包，同时发送syn+ack (ack是确认包) 包，进入syn\_recv状态
3. 客户端收到syn+ack包, 向服务器发送ack包，客户端和服务器进入ESTABLISHED状态，TCP链接建立成功

> 三次握手完毕后， 才开始传输数据

### 四次挥手

1. 客户端发送连接释放报文
2. 服务端收到连接释放报文，发出确认报文 (此时客户端已经没有数据要发送)
3. 客户端收到确认报文后，进入终止等待状态 
4. 服务端发完最后的数据后，向客户端发送连接释放报文
4. 客户端收到释放报文后，必须发出确认
5. 服务端收到确认报文后，立即进入CLOSED状态, 此次TCP连接结束。

## 网络攻击及预防

### xss

xss (cross-site script) 跨站脚本攻击

1. 攻击者在目标网站上注入恶意脚本，使之在用户浏览器运行。
2. 用户浏览器渲染DOM树的时候，执行了不可预期的JS脚本，从而发生安全问题。

**预防**

* url参数使用encodeURLComponent进行转义 然后decodeURLComponent进行解义
* 尽量不要使用innerHTML插入内容
* 使用特殊符号，标签转义符

### csrf

csrf(cross-site request forgery) 跨站请求伪造。

1. 用户C打开浏览器，访问服务器A，服务器A返回给C用户cookie等信息
2. 用户未退出A时，打开另一个网页B 3，B收到用户请求后，返回攻击性代码，发出另一个请求访问A
3. 攻击性代码携带用户**Cookie**信息向A发出请求，导致B网站的恶意代码被执行。

**预防**

1. 添加验证码
2. 使用token 服务端收到用户请求后，生成一个token，加密后返回给用户。 用户需要携带这个token再次提交请求。 服务端验证token是否正确。
