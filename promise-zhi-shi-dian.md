# 😎 Promise知识点

## Promise

> Promise是一个构造函数，可以进行对象的实例化,多用于处理> 异步任务(AJAX,文件IO,setTimeout)

### Promise的作用

* Promise支持链式调用，可以解决回调地狱的问题
* 指定回调函数的方式更加灵活

```javascript
const p = new Promise((resolve, reject) => {
  let a;
  if (a < 30) {
    resolve("OK");
  } else {
    reject("Error");
  }
});
p.then(
  (value) => {
    console.log(value);
  },
  (error) => {
    console.warn(error);
  }
);
```

\*\*注意：\*\*Promise定义时内部的函数是同步调用的，即立即执行(里面放着异步函数仍然是异步)

### Promise对象的状态

> 是Promise实例对象的一个属性 **PromiseState**

* pending
* resolved / fulfilled的Promise
* rejected \*\*注意:\*\*Promise对象的状态只能改变一次

### Promise对象的值

> 是Promise实例对象的另一个属性 **PromiseResult**,他保存着对象成功/失败的结果

### Promise对象的几种方法

`Promise.prototype.then` 进行异步调用，处理上一个Promise结果 `Promise.prototype.catch`与`then`类似，但只能进行失败的回调

### Promise原型的几种方法

`Promise.resolve()`

* 如果传入的参数为非Promise类型的对象，则返回的结果为成功的Promise对象，且返回对象的值为该参数。
* 如果传入的参数为Promise对象，则参数对象的结果决定了resolve的结果。

`Promise.reject()`

* 与resolve同理

`Promise.all(promises)`

* 返回一个Promise对象
* 传入一个包含n个promise对象的数组
* 传入的数组中**所有Promise**都成功才成功，有一个失败就直接失败

`Promise.race(promises)`

* 返回一个Promise对象
* 该方法由数组中**第一个改变状态的Promise对象**决定，他为fulfilled，返回的对象状态就是fulfilled；它为rejected，返回的对象就是rejected

### 改变Promise对象状态的3种方式

```
1. resolve函数
2. reject函数
3. throw抛出错误    `throw '错误'`
```

**注意：** 当Promise对象改变后，对应的回调函数都会执行

### 执行then方法后返回的Promise对象的状态

```
1. 抛出错误 => rejected
2. 返回结果是一个非Promise对象
    return 521;   =>  fulfilled
3. 返回的是一个Promise对象 => 取决于该Promise的状态
```

### Promise的异常穿透

可以在整个then的链条最后捕获错误

```javascript
p.then(v=>{}).then(v=>{}).then(v=>{})
.catch()
```

### 中断Promise的链式调用

* 只有一种方式： 返回一个pending状态的Promise对象,状态不改变自然不会往下运行.

```javascript
return new Promise(()=>{});
```

### 手写Promise.all

```javascript
/**
 * Promise.all的封装
 * @param {Promise []} promises
 * @returns
 */
Promise.all = function (promises) {
  return new Promise((resolve, reject) => {
    let count = 0;
    let arr = [];
    for (let i = 0; i < promises.length; i++) {
      promises[i].then(
        (v) => {
          // 得知对象状态是成功的
          // 每个promise对象都成功，就resolve
          count++;
          arr[i] = promises[i];
          if (count === promises.length) {
            resolve(arr);
          }
        },
        (r) => {
          reject(r);
        }
      );
    }
  });
};
```

### 手写Promise.race

```javascript
/**
 * Promise.race的封装
 * @param {Promise []} promises
 */
Promise.race = function (promises) {
  return new Promise((resolve, reject) => {
    for (let i = 0; i < promises.length; i++) {
      promises[i].then(
        (v) => {
          // 谁先运行 谁就决定了结果
          // 修改返回对象的状态为[成功]
          resolve(v);
        },
        (r) => {
          // 修改返回对象的状态为[失败]
          reject(r);
        }
      );
    }
  });
};
```

***

## async await

### async 函数

1. `async`函数返回的结果是一个**Promise**对象(返回状态与**then**函数类似)

* 如果返回值是一个非`Promise`类型的数据，结果就是一个成功的`Promise`对象，return写什么，成功的结果值就是什么
* 如果返回值是一个`Promise`对象，则取决于该Promise对象的状态
* 抛出异常 就变成rejected

```javascript
async function test() {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve();
      }, 2000);
    });
}
let res = test();
console.log(res); // fulfilled的Promise
```

***

### await 表达式

1. `await`右侧的表达式一般为promise对象，但也可以是其他的值
2. 如果表达式是`Promise`对象，`await`返回的是`Promise`**成功的值**
3. 如果表达式是其他值，直接将此值作为`await`的返回值 _本质目的是拿到Promise对象的结果_

#### 注意

1. `await`必须写在`async`函数中，但`async`函数中可以没有`await`
2. 如果`await`的`Promise`失败了，则要通过`try catch`处理

```javascript
async function test() {
  let p = new Promise((resolve, reject) => {
    // 为成功的情况
    // resolve("OK");
    reject("Error");
  });
  try {
    let res = await p;
  } catch (e) {
    console.log(res);
  }
}
```

***

### await与async联合使用

用Promise封装一个ajax请求

```javascript
function sendAjax(url) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.responseType = "json";
    xhr.open("GET", url);
    xhr.send();
    xhr.onreadystatechange = function () {
      if (xhr.readyState === 4) {
        if (xhr.status >= 200 && xhr.status < 300) {
          resolve(xhr.response);
        } else {
          reject(xhr.status);
        }
      }
    };
  });
}
let a = document.getElementById("a");
a.addEventListener("click", async function () { // 如果用await 一定要有async
  let a = await sendAjax("https://api.apiopen.top/api/getImages", {
    type: "animal",
    page: 0,
    size: 10,
  });
  console.log(a);
});
```

### async和await的作用

使用同步的书写方式来达到异步代码的调用效果。
