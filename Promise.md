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
