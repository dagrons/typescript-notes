# about js

## type in js 
js没有静态类型检查系统, 所有对象都是object(string, number, boolean, undefined不算对象), 所有object都可以看作是一个可以动态增删方法和属性的字典, js提供的primitive类型包括: Number, String, Array, Object, Function, null, undefined, 严格来说undefined不算类型, 它只是一个特殊的返回值, 其中Function是callable的object, Array也是Object, 只不过Array的键类型只能是整数, 其实在js的实现中, Array就是用Object实现的, null也是object, 这一点值得注意, 下面的的例子可以说明这一点

```javascript
a = []
typeof a === "object" // true
typeof a === "array" // false because no array type!
typeof a === typeof Array() // true because both are type of object
typeof a === "function" // false

a = "hello"
typeof a === "object" // false
typeof a === "string" // true
typeof a === "function" // false
typeof new String("sadas") === "object" // true

a = 1
typeof a === "object" // false
typeof a === "number" // true
typeof a === "function" // false
typeof new Number(1) === "object" // true

a = true
typeof a === "boolean" // true
typeof new Boolean(true) === "object" // true

a = () => {}
typeof a === "object" // false
typeof a === "function" // true

typeof Object === "function" // true, js中function is first-order 

typeof null === "object" // amazing!
typeof undefined === "undefined" 
```
**NOTE: js中只有number, string, object, function, undefined, 没有array和null**
**NOTE: 重要的事情说三遍, typeof null === "object", typeof [] === "object" !!!**

## function vs object
function in js are actually objects in js, the only difference that function objects have is that they are callable.

## this in js
"this" in function depends on who called the function(if not been bound), while "this" in arrow function is lexical binding(do not depend on who called the function,

function中this取决于caller, 而arrow function中this取决于lexical scope(dynamic binding vs lexical binding)

下面的例子可以帮助理解它们
eg:
```javascript
function a() {return this}
a(); // Window

b = {}; 
b.a = a;
b.a(); // {a: f} // b.a() == a.bind(b)()

a.bind(b);
a(); // Window

a = a.bind(b); 
a(); // {a: f} 

a = () => {return this}
b.a = a;
b.a(); // Window

a.bind(b)(); // Window // bind do not work on arrow function!
```

**NOTE: 一般考虑使用arrow function, 如果是为object定义方法, 使用function, 但如果传递该方法时, 需要谨慎, 如下, 将方法作为值传递时, 记得绑定this!**
```javascript
b = {
    a: function() {return this}
}
b.a() // {a: f}

a = b.a
a() // Window

b = {
    a: function() {return this}.bind(this)
}
b.a() // Window

b = {
    a: function() {return this}.bind(b) // recommend one! 
}
b.a() // {a: f}
a = b.a 
a() // {a: f}
```
## 其他知识点
1. 在js中被视为false的值
```javascript
0
NaN
""
0n // bigint
null
undefined 
```

