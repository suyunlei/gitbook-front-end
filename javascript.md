# 😁 JavaScript

## JavaScript数据类型

**值类型** ：

* Number
* String
* Symbol
* Boolean
* null
* undifined
* BigInt

**引用类型**:

* Object
* Array, Map, Set等
* Function

注意： BigInt和Number之间转换会损失精度。 Symbol常常用来当作对象属性名，表示唯一值。

## JavaScript位运算

> 在JavaScript内部，数值都是64位浮点数存放的，但是做位运算的时候，是以32位带符号整数进行运算的，并且返回值也是一个带符号32位整数。

### 1. 按位与 &

每一位都为1，即为1，否则为0 e.g:

```
a = 2;   00000010
b = 3;   00000011
a & b    00000010
```

> 超级巧妙的用法 即 统计n用二进制表示有几个1

```javascript
function getOne(n) {
  let count = 0;
  for(let i = 0;i <= n;i++){
    let j = i;
    while(j){
      j = j & (j-1);
      count++;
    }
  }
  return count;
}
```

### 2. 按位或 |

只要有一位是1，即为1。两个都为0才是0 e.g:

```
a = 2; 00000010
b = 3; 00000101
a | b  00000111
```

### 3. 按位异或 ^

只有一个1的时候才是1，否则都为0 e.g:

```
a = 2; 00000010
b = 6; 00000110
a^b    00000100
```

> 超级巧妙的用法 交换两个值

```javascript
a = 3;
b = 4;
a ^= b;
b ^= a;
a ^= b;
console.log(a); // 4
console.log(b); // 3
```

### 4. 按位非 \~

对位求反，也就是0变1，1变0

```
a = 2; 00000010
~a   ; 11111101
```

**注意** 内存中，首位是符号位 0 是正数 1 是负数 e.g.

```
11111101 是多少？
方案：取反加1

1. 00000010
2. 00000011
即11111101应该是 - 3
```

# 严格模式
不支持严格模式与支持严格模式的浏览器在执行严格模式代码时会采用不同行为。严格模式代码和非严格模式代码可以共存，因此项目脚本可以*渐进式*地采用严格模式。
``` javascript
// 整个脚本都开启严格模式的语法
"use strict";
var v = "Hi!  I'm a strict mode script!";
```
但这样会产生合并问题，建议按照一个个**函数**去开启严格模式
``` javascript
function strict() {
  // 函数级别严格模式语法
  'use strict';
  function nested() {
    return "And so am I!";
  }
  return "Hi!  I'm a strict mode function!  " + nested();
}
```

## 会将过失错误转化为异常
1. 严格模式下无法意外的创建全局变量
``` javascript
"use strict";
                       // 假如有一个全局变量叫做mistypedVariable
mistypedVaraible = 17; // 因为变量名拼写错误
                       // 这一行代码就会抛出 ReferenceError

```
2. 严格模式会引擎静默失败的赋值操作会抛出异常。
    - 给不可写属性赋值
    - 给只读属性赋值
    - 给不可扩展对象的新属性赋值
``` javascript
"use strict";

// 给不可写属性赋值
var obj1 = {};
Object.defineProperty(obj1, "x", { value: 42, writable: false });
obj1.x = 9; // 抛出 TypeError 错误

// 给只读属性赋值
var obj2 = { get x() { return 17; } };
obj2.x = 5; // 抛出 TypeError 错误

// 给不可扩展对象的新属性赋值
var fixed = {};
Object.preventExtensions(fixed);
fixed.newProp = "ohai"; // 抛出 TypeError 错误

```
3. 试图删除不可删除的属性时会抛出异常
``` javascript
"use strict";
delete Object.prototype; // 抛出 TypeError 错误
```
4. 在同一个对象内属性名重复会抛出异常
``` javascript
"use strict";
var o = { p: 1, p: 2 }; // !!! 语法错误
```
5. 严格模式下要求函数的参数名唯一
``` javascript
const add = function (a, a, c) {
  return a + a + c;
};
console.log(add(1, 2, 3)); // 输出 7


"use strict";
const add = function (a, a, c) {
  return a + a + c;
};
console.log(add(1, 2, 3)); // 会报错
```
6. 严格模式禁止8进制的语法数字

## 简化变量的使用
1. 严格模式禁用with
2. 严格模式eval不再为上层作用域引入新变量
3. 严格模式禁止删除声明变量
4. 严格模式下，参数值不会随着arguments的对象的变化而变化
``` javascript
// 不用严格模式
function f(a) {
  a = 42;
  return [a, arguments[0]];
}
var pair = f(17);
console.log(pair); //42,42

// 使用严格模式
function f(a) {
  "use strict";
  a = 42;
  return [a, arguments[0]];
}
var pair = f(17);
console.log(pair); // 42,17
```

# import的使用
file.js
``` javascript
function getJSON(url, callback) {
  let xhr = new XMLHttpRequest();
  xhr.onload = function () {
    callback(this.responseText)
  };
  xhr.open('GET', url, true);
  xhr.send();
}

export function getUsefulContents(url, callback) {
  getJSON(url, data => callback(JSON.parse(data)));
}
```
main.js
``` javascript
import { getUsefulContents } from '/modules/file.js';

getUsefulContents('http://www.example.com',
    data => { doSomethingUseful(data); });
```

# Object对象遍历的方式
1. `for...in...`遍历*自身的*和*继承的* **可枚举属性** 不含Symbol属性
2. `Object.keys()`遍历，返回一个数组包括对象的所有可枚举属性(不含*继承的*和*不可枚举的*)
3. `Object.getOwnPropertyNames(obj)`返回一个数组,包括对象自身的所有属性(不含Symbol, 但含有*不可枚举的*)
4. 使用`Reflect.ownKeys(obj)`，返回一个数组，包含对象自身所有的属性 (不管属性名是Symbol或字符串，也不管是否可枚举)

## 对象遍历的顺序
1. 会先按照正整数的规则遍历正整数的部分， 不管是(7,8,"3","9") 但"-1", "1.3"会按照插入的顺序
2. 接下来会按照插入顺序遍历剩下的字符串
3. 最后再按照插入顺序遍历Symbol类型

``` javascript
let o = {};
o[Symbol("s2")] = "foo";
o[Symbol("s1")] = "bar";
o.b = "bbbbb";
o.a = "aaaaa";
o[2] = true;
o[1] = true;

Reflect.ownKeys(o); // ['1', '2', 'b', 'a', Symbol(s2), Symbol(s1)]
Object.getOwnPropertyNames(o); //  ['1', '2', 'b', 'a']
Object.getOwnPropertySymbols(o); // [Symbol(s2), Symbol(s1)]
```