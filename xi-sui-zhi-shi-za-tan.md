# 🤩 细碎知识杂谈

## 细碎知识杂谈

### 进程和线程

#### 进程

* 进程是是**内存**中运行的应用程序。
* 是**系统**进行资源调度和分配的基本单位。
* 实现了操作系统的并发。

#### 线程

* 线程是进程的**子任务**
* 是**CPU**调度和分派的基本单位
* 实现了进程内部的并发

#### 进程与线程的区别

1. **一个**进程可以包含**多个**线程，一个线程只能属于一个进程
2. 进程在执行过程中拥有独立的内存单元，而多个线程共享进程的内存
3. 进程是资源分配的最小单位，线程是CPU调度的最小单位
4. 进程之间不会相互影响，线程一个挂掉则整个进程挂掉
5. 进程切换的开销远大于线程切换的开销
6. 进程编程调试简单可靠性高，线程调试小队复杂

#### 进程间通信的方式

进程间通信主要包括**管道，系统IPC** 线程间可以直接读写**进程数据段**

***

***

### 事件委托

事件委托的本质上是把一个元素的事件委托到另一个元素上(通常是父元素或更外层元素)。 

**作用**

1. 减少内存消耗 假如要为li绑定事件，但多个id绑定多次事件极大的消耗内存。 故利用事件委托，把事件绑定给ul，再利用event.target判断点击哪个li，执行后续操作。

```html
<ul id="list">
  <li>item 1</li>
  <li>item 2</li>
  <li>item 3</li>
  ......
  <li>item n</li>
</ul>
```

2. 动态绑定事件 假如要动态生成li，可以把事件绑定在最外层

***
### 手写ajax
``` javascript
const getJSON = function (url) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open("GET", url, false);
    xhr.setRequestHeader("Content-Type", "application/json");
    xhr.onreadystatechange = function () {
      if (xhr.readyState !== 4) return;
      if (xhr.status === 200 || xhr.status === 304) {
        resolve(xhr.responseText);
      } else {
        reject(new Error(xhr.responseText));
      }
    };
    xhr.send();
  });
};
```

### 取消HTTP请求

1. `xhr.abort()`
2.  axios中利用`AbortController`

    ```javascript
    const controller = new AbortController();

    axios.get('/foo/bar', {
      signal: controller.signal
    }).then(function(response) {
      //...
    });
    // 取消请求
    controller.abort()
    ```

***

### 节流和防抖

节流：事件触发后，规定时间内，事件处理函数不能再次被调用。即在规定时间内，只能调用一次。 
防抖：多次触发事件，事件处理函数只执行一次，规定时间内，只有最后一次生效。

**使用**

* 节流： 记录时间，只有大于阈值时，才会进行第二次执行。
``` javascript
const handler = () => {
  console.log("某一个事件");
};
const throttle = (fn, delay) => {
  return function () {
    let lastTime = 0;
    let nowTime = Date.now();
    if (nowTime - lastTime > delay) {
      fn.apply(this);
      fn();
      lastTime = nowTime;
    }
  };
};
window.addEventListener("scroll", throttle(handler, 2000));
```

* 防抖： 每次创建一个延时器，并且每次都清楚上一次的延时器。重新设置新的延时器。
``` javascript
const handler = () => {
  console.log("某一个事件");
};
const debounce = (fn, time) => {
  let timeout = null;
  return function () {
    clearTimeout(timeout);
    timeout = setTimeout(fn, time);
  };
};
window.addEventListener("scroll", debounce(handler, 2000));
```

***

### Cookie, Session , Token的区别

#### Cookie

cookie是保存在**本地**的数据。cookie由**服务器生成**，发送给浏览器，浏览器把cookie保存到某个目录下的文本文件内，下一次请求同一网站时, 按照一定的原则在后台自动把该cookie发送给服务器。 cookie是服务端保存在数据库，客户端保存在cookie文件中。 cookie验证是**有状态**的。

cookie的内容主要包括：名字、值、**过期时间**、路径和域。

不同的浏览器之间可以公用一个cookie。

#### Session

Session保存在**服务器**，也是为了存放用户信息。用户离开网站后Session被销毁。 客户端存储的其实是`session id`，保存这个session id的方式可以采用`cookie`. 在会话状态中，客户端用`session id`标明身份

本质上其实是Cookie+Session一起协同工作的

#### Token

token 也称作令牌，本质上是一个**32位字符串**在客户端一般存放于localStorage，也可以存放在cookie，或sessionStorage中。服务器不需要存储。 客户端每次发送请求的时候带上这个token，服务器只需要解析这个token是否有效即可。 token验证是**无状态**的。

token可以防护**csrf**，cookie和session不行

***

### 图片懒加载的实现原理

预先将图片真实的src放在我们自定义的属性里面（比如data-src），当图片出现在了我们的视口范围之内的时候，再把data-src赋值给src属性，完成图片加载。

```html
// 页面初始化时
<img data-src="https://img11.360buyimg.com/pop/s590x470.jpg.webp" />

// 当图片出现在了视口范围之内 修改为 : 
<img data-src="https://img11.360buyimg.com/pop/s590x470.jpg.webp" src="https://img11.360buyimg.com/pop/s590x470.jpg.webp"/>
```

实现方法： 可以计算图片到页面顶端的距离。

***

### 柯里化

> 柯里化可以让我们给一个函数传递较少的参数得到一个**已经记住了某些固定参数**的新函数

```javascript
// 下面这段代码是解决了不纯的函数的问题，但是里面出现了硬编码
function checkAge (age) { 
    let mini = 18
    return age >= mini 
}


// 普通的纯函数
function curry(f) {  // curry(f) 执行柯里化转换
  return function(a) {
    return function(b) {
      return f(a, b);
    };
  };
}
// 用法
function sum(a, b) {
  return a + b;
}
let curriedSum = curry(sum);
alert( curriedSum(1)(2) ); // 3
```

### Webpack

前端工程化可以引出webpack —— `webpack.config.js` 能提供的**作用**包括：

* 代码压缩混淆
* 处理浏览器端javascript的兼容性
* 性能优化

```javascript
module.exports = {
    // 指定构建模式，有两个参数可选
    // 开发阶段使用development,因为打包速度快
    // 上线阶段使用production,因为项目上线需要文件体积小
    mode:'development'
}
```

## 浏览器输入URL到页面加载全过程

1. 输入URL
2. 查找缓存
3. DNS域名解析，根据域名解析IP地址 - DNS是基于**UDP**协议的; 本地DNF服务器 - 根域名服务器 - 一级域名服务器 - 二级域名服务器
4. 建立TCP连接 - 三次握手
5. 客户端发送HTTP请求
6. 服务器接收HTTP请求并返回响应
7. 断开TCP连接 - 四次挥手
8.  浏览器渲染

    * 构建DOM树
    * 构建CSS规则树
    * 构建render树
    * 布局 layout
    * 绘制
    * JS引擎解析

    > 是一个渐进的过程，不必等待全部HTML文件解析后才渲染，会尽快将内容渲染

## 内存泄漏

常见的内存泄漏的几种方式：

1. 闭包
2. 全局变量
3. DOM元素的引用
4. 定时器

## ES6及后续版本 新特性

1. let const 变量结构赋值
2. ()=>{}
3. Promise
4. \`${}\`
5. ...rest 用于获取函数的实参
6. Symbol
7. 迭代器 for ... of ...
8. Set, Map
9. 生成器
10. class类
11. \*\*
12. async await

## class与构造函数的区别
构造函数会变量提升，即可以先使用后声明。
而class必须先声明，后使用。

