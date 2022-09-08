# JavaScript

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
