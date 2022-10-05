# ShallowCopy and DeepCopy of Objects
**Shallow Copy**: Cursor just points to the memory address being copied. And if the value of original address changes, then the shallow copy of the resulting object will change accordingly.
**Deep Copy**: Create a new memory address in the computer to hold the copied objects.

## ShallowCopy Ways
1. `Obejct.assign(target, a)`
    - It just creates a new object at the root property (the first level of the object), but it is still a shallow copy if the value of the property is still an object.
    - Properties that are not **enumerable** are not copied to the target object
    - Symbol types can be copied because Symbol is traversable
    - Does not copy properties that inherited from objects
``` javascript
let target = {
  e: 1,
};
let a = {
  a: 2,
  b: {
    c: 500,
  },
};
Object.assign(target, a);
console.log(target); // {e:1,a:2,b:{c:2000}}  a won't change in target Obejct
a.a = 1000;
a.b.c = 2000;
console.log(target); // {e:1,a:2,b:{c:2000}}   c will change in target Object
```

2. Spread Syntax(...), slice, concat,etc. also only deep copy one level. (Just like Object.assign(a,b) function)

## DeepCopy Ways
1. Loop + Recursion
    - Loop is to loop through each key, recursive is to determine if the value corresponding to the key is also an object
    - Only deep copies of objects and arrays can be implemented
    - **for... .in** cannot get a Symbol type key, while **Reflect** can.
        ``` javascript
        let c = Symbol("c");
        let obj = {
          a: 1,
          b: 2,
        };
        obj[c] = 3;
        Reflect.ownKeys(obj).forEach((item) => {
          console.log(item);
        });
        ```
2. `JSON.stringify`
    - If there is a **function, undifined, symbol** in the value of the copied object, the key-value pair will disappear from the JSON string after serialization by JSON.stringify()
    - Cannot copy **non-enumerable properties**, cannot copy object **prototype chains**
    - **Date** reference type becomes a string
    - **RegExp** reference types will turn them into empty objects
    - An object containing **NaN, Infinity, -Infinity** will be serialized to **null**
    - Loop applications that cannot be copied (i.e. obj[key] = obj)
    ``` javascript
    function deepClone(obj) {
      let temp = JSON.stringify(obj);
      return JSON.parse(temp);
    }
    let obj1 = {
      a: {
        b: 1,
      },
    };
    Object.defineProperty(obj1, "key", {
      value: "value",
      enumerable: false,
    });
    let obj2 = deepClone(obj1);
    obj1.a.b = 2;
    console.log(obj2); //{a:{b:1}}   最后定义的key: value 不会被拷贝
    ```
