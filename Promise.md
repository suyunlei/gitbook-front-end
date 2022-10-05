# Promise

> Promise is a **constructor** that instantiates objects, mostly for handling asynchronous tasks (AJAX, file IO, setTimeout)

## What Promise Does
- Promise supports chained calls, and can solve callback hell.
- More flexibility in the way callback functions are specified

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

> The function inside Promise is called **synchronously** when it is defined, i.e. it is executed immediately (an asynchronous function placed inside is still asynchronous)

``` javascript
new Promise(resolve=>{
  resolve(3);
  console.log(1);})
  .then(num=>{
    console.log(num);
  });
  console.log(2);
  /// 123
```

## PromiseState

> PromiseState is one of attributes of Promise instance object.

- pending
- resolved / fulfilled
- rejected

**Note**
The status of Promise instance can only change once.

## PromiseResult

> Another attribute on Promise Instance
It describes the result of Promise Instance's success / failure.

## Functions on Promise.prototype

`Promise.prototype.then`
- Asynchronous call to process the previous Promise Instance result

`Promise.prototype.catch`
- Like 'then', but only failed callbacks can be made

## Functions on Promise

`Promise.resolve()`

 - If the argument passed in is an object of type other than Promise, the result returned is a successful Promise object and the value of the returned object is that argument.
 - If the argument passed in is a Promise object, the result of the argument object determines the result of resolve function.

`Promise.reject`

 - Like resolve

`Promise.all(promises)`

 - Return a Promise object
 - Argument is an **array** of n Promise objects
 - Return Success Promise object is only achieved if **all Promise** in the incoming array succeed. Otherwise, Return a failed Promise object.

`Promise.race(promises)`

The race function returns a Promise that is settled the same way (and takes the same value) as the first promise that settles amongst the promises of the iterable passed as an argument.

### Three ways to change the state of Promise

1. resolve function
2. reject function
3. throw

**Note**
When the Promise state changes, the corresponding callback functions are executed

### The PromiseState after the 'then' function excuting

1. throw  = > rejected
2. return a non-Promise
    return 521;   = > fulfilled
3. return Promise
    = > depends on the previous PromiseState

### Error handling with Promises

> Errors can be caught at the end of the whole 'then' chain

```javascript
p.then(v=>{}).then(v=>{}).then(v=>{})
.catch()
```

### Interrupting Promise chain calls

- Only 1 way: 
    Return a `Promise` in the pending state, the state does not change and it will not run further.


``` javascript
return new Promise(()=>{});
```

## Handwritten Promise.all() method

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

## Handwritten Promise.race() method

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

