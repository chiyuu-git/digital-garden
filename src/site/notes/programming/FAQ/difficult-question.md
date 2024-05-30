---
{"aliases":["疑难杂症"],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2021-12-22-Wed, 2:52:17 pm","date-modified":"2024-05-30-Thu, 3:55:49 pm","permalink":"/programming/FAQ/difficult-question/","dgPassFrontmatter":true}
---


# 题目

```js
let a = 1;
(function a(){
    a = 2;
    console.log(a);
})()
console.log(a);
```

函数内的 console

```
ƒ a(){
    a = 2;
    console.log(a);
}
```

外部的 console

```
1
```

# 题目二

```ts
var b = function c () {
  console.log(c) //可见
}
b()
console.log(c) //Uncaguht ReferenceError： c is not defined
```

```ts
foo = function xxx (){
  console.log(123)
}

foo()
// xxx() //not defined
function ccc (){
  console.log(456)
}

bar = ccc
bar()
ccc()

// 调用之后赋值
ddd = ccc()
console.log(ddd); //undefined
```

# 题目三

```ts
function functions(flag) {
    if (flag) {
      function getValue() { return 'a'; }
    } else {
      function getValue() { return 'b'; }
    }

    return getValue();
}
```

```ts
function functions(flag) {
    function getValue() {return(flag)?'a':'b'; }
    return getValue();
}
// 原函数的问题是因为不存在块级作业域getValue函数被重写
```

# 面试题

```js
let x, {x:y=1} = {x} ; 
// y 1，x 为 undefined，使用了 默认值 1
// x ?
```

# 题目

let endRecord = startRecord = null

> 省略了声明，startRecord 自动上升了成了全局变量，导致出错

# This

# This

可见 `new` 对 `this` 的影响比 `bind` 优先级要高，

`g` 虽然通过 `bind` 绑定了 `this` 指向的对象为 `{a:1}`，

但是使用 `new g` 调用的时候，`this` 仍然指向以 `f` 为构造函数的实例。

```js
let a = {
  b:{
    c(){
      console.log(this)
    }
  }
}

console.log(a.b.c()); // {c: }
```

parameter 是指函数定义中参数，而 argument 指的是函数调用时的实际参数。简略描述为：parameter=形参 (formal parameter)， argument=实参 (actual parameter)。

# 解析

a=2 执行的不是给全局变量 a 赋值为 2，而是想要将自执行函数的 name 'a', 更改为 2，但这句话在 console.log 后证实未执行，即这句代码被忽略了。

没错。在第一行加一句代码 "use strict"; 在严格模式下执行就能看到报错 ：Uncaught TypeError: Assignment to constant variable （常数变量赋值），

而这个错误在正常模式下是不会执行的，也就是静默错误，直接忽略执行。

所以打印的结果是 f a(){...}

为什么严格模式下报错，因为 Function.name 的 Writable 属性默认是不可写的。（但可通过 Object.defineProperty() 更改）

```js
let descriptor = Object.getOwnPropertyDescriptor(a,"name")
    console.log(descriptor.value)  // a
    console.log(descriptor.writable) // false
```
