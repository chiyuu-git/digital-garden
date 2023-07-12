---
{"dg-publish":true,"permalink":"/programming/font-end/framework/typescript/ts-basic/"}
---


2023-04-23

[TypeScript 官方手册-英文](http://www.typescriptlang.org/docs/handbook/tsconfig-json.html)

typescript 入门教程：<https://ts.xcatliu.com/> 与我目前整理的结构一致 done

[知乎文章：Effective Typescript：使用Typescript的n个技巧](https://zhuanlan.zhihu.com/p/104311029)

沈毅的分享：<http://wiki.baidu.com/pages/viewpage.action?pageId=1408233236>

<https://juejin.cn/post/6844904167840940039>

<https://zhuanlan.zhihu.com/p/104311029>

sucheng:<https://github.com/joye61/typescript-tutorial>

bianxuebianlian:<https://zhuanlan.zhihu.com/p/393156734>

[深入理解 TypeScript](https://jkchao.github.io/typescript-book-chinese/)

【万字长文】深入理解 TypeScript 高级用法：<https://zhuanlan.zhihu.com/p/136254808>

# 基础

## 什么是 TypeScript

Typed JavaScript at Any Scale

添加了类型系统的 JavaScript，适用于任何规模的项目

### TypeScript 是静态类型

类型系统按照「类型检查的时机」来分类，可以分为动态类型和静态类型。

动态类型是指在运行时才会进行类型检查，这种语言的类型错误往往会导致运行时错误。JavaScript 是一门解释型语言 [what is typescript](https://ts.xcatliu.com/introduction/what-is-typescript.html#link-4), 没有编译阶段, 所以它是动态类型, 以下这段代码在运行时才会报错：

```js
let foo = 1;
foo.split(' ');
// Uncaught TypeError: foo.split is not a function
// 运行时会报错（foo.split 不是一个函数），造成线上 bug
```

静态类型是指编译阶段就能确定每个变量的类型，这种语言的类型错误往往会导致语法错误。TypeScript 在运行前需要先编译为 JavaScript，而在编译阶段就会进行类型检查，所以 **TypeScript 是静态类型**，这段 TypeScript 代码在编译阶段就会报错了：

```ts
let foo = 1;
foo.split(' ');
// Property 'split' does not exist on type 'number'.
// 编译时会报错（数字没有 split 方法），无法通过编译
```

### TypeScript 是弱类型

类型系统按照「是否允许隐式类型转换」来分类，可以分为强类型和弱类型。

以下这段代码不管是在 JavaScript 中还是在 TypeScript 中都是可以正常运行的，运行时数字 `1` 会被隐式类型转换为字符串 `'1'`，加号 `+` 被识别为字符串拼接，所以打印出结果是字符串 `'11'`。

```js
console.log(1 + '1');
// 打印出字符串 '11'
```

TypeScript 是完全兼容 JavaScript 的，它不会修改 JavaScript 运行时的特性，所以 **它们都是弱类型**。

作为对比，Python 是强类型，以下代码会在运行时报错：

```py
print(1 + '1')
# TypeError: unsupported operand type(s) for +: 'int' and 'str'
```

若要修复该错误，需要进行强制类型转换：

```py
print(str(1) + '1')
# 打印出字符串 '11'
```

> 强/弱是相对的，Python 在处理整型和浮点型相加时，会将整型隐式转换为浮点型，但是这并不影响 Python 是强类型的结论，因为大部分情况下 Python 并不会进行隐式类型转换。相比而言，JavaScript 和 TypeScript 中不管加号两侧是什么类型，都可以通过隐式类型转换计算出一个结果——而不是报错——所以 JavaScript 和 TypeScript 都是弱类型。

> 虽然 TypeScript 不限制加号两侧的类型，但是我们可以借助 TypeScript 提供的类型系统，以及 ESLint 提供的代码检查功能，来限制加号两侧必须同为数字或同为字符串 [](https://ts.xcatliu.com/introduction/what-is-typescript.html#link-5) @link。这在一定程度上使得 TypeScript 向「强类型」更近一步了——当然，这种限制是可选的。

这样的类型系统体现了 TypeScript 的核心设计理念：在完整保留 JavaScript 运行时行为的基础上，通过引入静态类型系统来提高代码的可维护性，减少可能出现的 bug。

### 编译时检查

TypeScript 只会在编译时对类型进行静态检查, 如果发现有错误, 编译的时候就会报错. 而在运行时, 与普通的 JavaScript 文件一样, 不会对类型进行检查

如果我们需要保证运行时的参数类型，还是得手动对类型进行判断

## 类型总览 @@@

| 类型    | 例子            | 描述                           |
| ------- | --------------- | ------------------------------ |
| number  |                 | 任意数字                       |
| string  |                 | 任意字符串                     |
| boolean |                 | 布尔值 true or false            |
| 字面量  | 其本身          | 限制变量的值就是该字面量的值   |
| any     |                 | 任意类型                       |
| unkown  |                 | 类型安全的 any                  |
| void    | 空值 (undefined) | 没有值（或 undefined）         |
| never   | 没有值          | 不能是任何值                   |
| object  |                 | 任意的 js 对象                   |
| array   |                 | 任意 js 数组                     |
| tuple   |                 | 元组，TS 新增类型，固定长度数组 |
| enum    |                 | 枚举，TS 中新增类型             |

关键的是新增类型的使用场景

字面量也可以是类型

Fucntion 类型是大写的？是用箭头描述的那种，单纯的一个 Function 是没有意义的

## 原始数据类型

JavaScript 的类型分为两种：原始数据类型（[Primitive data types](https://developer.mozilla.org/en-US/docs/Glossary/Primitive)）和对象类型（Object types）。

原始数据类型包括：布尔值、数值、字符串、`null`、`undefined` 以及 [ES6 中的新类型 `Symbol`](http://es6.ruanyifeng.com/#docs/symbol)。

本节主要介绍 **前五种** 原始数据类型在 TypeScript 中的应用。

### 布尔值

布尔值是最基础的数据类型，在 TypeScript 中，使用 `boolean` 定义布尔值类型：

```ts
let isDone: boolean = false;
// 编译通过
// 后面约定，未强调编译错误的代码片段，默认为编译通过
```

注意，使用构造函数 `Boolean` 创造的对象 **不是** 布尔值：

```ts
let createdByNewBoolean: boolean = new Boolean(1);

// index.ts(1,5): error TS2322: Type 'Boolean' is not assignable to type 'boolean'.
// 后面约定，注释中标出了编译报错的代码片段，表示编译未通过
```

事实上 `new Boolean()` 返回的是一个 `Boolean` 对象：

```ts
let createdByNewBoolean: Boolean = new Boolean(1);
```

直接调用 `Boolean` 也可以返回一个 `boolean` 类型：

```ts
let createdByBoolean: boolean = Boolean(1);
```

在 TypeScript 中，`boolean` 是 JavaScript 中的基本类型，而 `Boolean` 是 JavaScript 中的构造函数，用作类型则是布尔值对象。其他基本类型（除了 `null` 和 `undefined`）一样，不再赘述。

### 数值

使用 `number` 定义数值类型：

```ts
let decLiteral: number = 6;let hexLiteral: number = 0xf00d;
// ES6 中的二进制表示法let binaryLiteral: number = 0b1010;
// ES6 中的八进制表示法let octalLiteral: number = 0o744;let notANumber: number = NaN;let infinityNumber: number = Infinity;
```

编译结果：

```js
var decLiteral = 6;var hexLiteral = 0xf00d;
// ES6 中的二进制表示法var binaryLiteral = 10;
// ES6 中的八进制表示法var octalLiteral = 484;var notANumber = NaN;var infinityNumber = Infinity;
```

其中 `0b1010` 和 `0o744` 是 [ES6 中的二进制和八进制表示法](http://es6.ruanyifeng.com/#docs/number#二进制和八进制表示法)，它们会被编译为十进制数字。

### 字符串

使用 `string` 定义字符串类型：

```ts
let myName: string = 'Tom';let myAge: number = 25;
// 模板字符串let sentence: string = `Hello, my name is ${myName}.I'll be ${myAge + 1} years old next month.`;
```

编译结果：

```ts
var myName = 'Tom';var myAge = 25;
// 模板字符串var sentence = "Hello, my name is " + myName + ".\nI'll be " + (myAge + 1) + " years old next month.";
```

### 空值 Void

JavaScript 没有空值（Void）的概念，在 TypeScript 中，可以用 `void` 表示没有任何返回值的函数：

```ts
function alertName(): void {
	alert('My name is Tom');
}
```

声明一个 `void` 类型的变量没有什么用，因为你只能将它赋值为 `undefined` 和 `null`：

```ts
let unusable: void = undefined;
```

### Never

表示永远没有值，永远不会返回结果

```js
function fn(): never{
	throw new Error('报错了')
}
```

报错时，连 undefined 都不返回

还有一情况是 死循环，也永远不会返回，笑了

本质是：函数不会执行完，因为执行完了怎么也会返回 undefined

### Null 和 Undefined

在 TypeScript 中，可以使用 `null` 和 `undefined` 来定义这两个原始数据类型：

```ts
let u: undefined = undefined;
let n: null = null;
```

`undefined` 类型的变量只能被赋值为 `undefined`，`null` 类型的变量只能被赋值为 `null`。

与 `void` 的区别是，`undefined` 和 `null` 是所有类型的子类型。也就是说 `undefined` 类型的变量，可以赋值给 `number` 类型的变量：

> 这个特性是可配置的

```ts
// 这样不会报错
let num: number = undefined;
```

```ts
// 这样也不会报错
let u: undefined;
let num: number = u;
```

而 `void` 类型的变量不能赋值给 `number` 类型的变量：

```ts
let u: void;let num: number = u;// index.ts(2,5): error TS2322: Type 'void' is not assignable to type 'number'.
```

### 装箱和拆箱

装箱是指将基本类型转化成包装器类型，拆箱是指将包装器类型转化成基本数据类型

## 任意值 Any & Unkown

### 概念

任意值（Any）用来表示允许赋值为任意类型

如果是一个普通类型，在赋值过程中改变类型是不被允许的：

```ts
let myFavoriteNumber: string = 'seven';
myFavoriteNumber = 7;
// index.ts(2,1): error TS2322: Type 'number' is not assignable to type 'string'.
```

但如果是 `any` 类型，则允许被赋值为任意类型。

```ts
let myFavoriteNumber: any = 'seven';
myFavoriteNumber = 7;
```

如果声明变量时不指定类型，也不赋值，则通过类型推断为 any

```js
let a;
```

显示、隐式的 any 都要尽量避免

### 任意值的属性和方法

在任意值上访问任何属性都是允许的：

```ts
let anyThing: any = 'hello';
console.log(anyThing.myName);
console.log(anyThing.myName.firstName);
```

也允许调用任何方法：

```ts
let anyThing: any = 'Tom';
anyThing.setName('Jerry');
anyThing.setName('Jerry').sayHello();
anyThing.myName.setFirstName('Cat');
```

> 只是说访问时允许的，但是有没有值，本质还是和 js 一样
>
> 由于是静态类型分析工具，因此 TS 并不会执行 JS 代码，但并不是说 TS 内部没有执行逻辑

原始数据类型也会有方法和属性：

+ typscript 下非 any 的访问不属于自己的属性和方法爆编译错误
+ 原生访问不属于自己的属性输出 undefiend，不属于自己的方法报错 `xxx is not a function`

可以认为，**声明一个变量为任意值之后，对它的任何操作，返回的内容的类型都是任意值**

### 避免 Any

通过 ts 设置, 避免 any

![ts-basic](ts-basic.md#未赋值的变量)

### Unknown

类型安全的 any，unknown 类型的变量不能直接复制给其他类型变量

```js
// 隐式any类型
let bar;
let str: string;bar = 10;
// bar 的类型是any，可以复制给任何类型变量， 编译不会报错str = bar;
```

```js
let foo: unknown;
foo = 10foo = truefoo = 'str';
let str: string;
// foo 的类型是 unknow，不可以赋值给其他类型str = foo;
// 通过类型断言解决 unknow str = foo as string;str = <string>foo;
```

```js
// Type 'unknown' is not assignable to type 'string'
```

## 类型推论 Type Inference

### 概念

如果没有明确的指定类型，那么 TypeScript 会依照类型推论（Type Inference）的规则推断出一个类型，包括：

1. 没有明确指定类型的变量
2. 没有明确指定类型的类的属性/方法
    1. 属性会被推断成具体的类型
    2. 方法会被推断成具体的函数

以下代码虽然没有指定类型，但是会在编译的时候报错：

```ts
let myFavoriteNumber = 'seven';
myFavoriteNumber = 7;
// index.ts(2,1): error TS2322: Type 'number' is not assignable to type 'string'.
```

事实上，它等价于：

```ts
let myFavoriteNumber: string = 'seven';
myFavoriteNumber = 7;
// index.ts(2,1): error TS2322: Type 'number' is not assignable to type 'string'.
```

TypeScript 会在没有明确的指定类型的时候推测出一个类型，这就是类型推论。

### 未赋值的变量

如果定义的时候没有赋值，不管之后有没有赋值，都会被推断成 `any` 而完全不被类型检查

```ts
let something;
something = 'seven';
something = 7;
something.setName('Tom');
```

等价于

```ts
let something: any;
something = 'seven';
something = 7;
something.setName('Tom');
```

### 函数返回值是否会有类型推论？

没有，函数的返回值意义在于被赋值，如果被赋值了，那么被赋值的变量会有类型推断

### 类型推论对继承的影响

```js
class Element extends SanNode() {
    createEl(): View {
        return new View(this);
    }
}
class ListViewElement extends Elemeent() {
    createEl() {
        return new ListView(this);
    }
}
class OverflowScrollViewEelment extends ListViewElment() {
    createEl() {
        retuen new OverflowScrollView(this);
    }
}
```

原因是 ListViewElment 声明时 ts 进行了类型推断，createEl 变成了 `() => Listview`

所以继承之后返回 OverflowScrollView 就提示出错了，改正的方法就是给 `return new ListView(this) as View`

这样类型推断就还是返回 View 类即可，overflowScrollview 也就 ok 了

## 联合类型 Union Types

### 概述

联合类型（Union Types）表示取值可以为多种类型中的一种

联合类型使用 `|` 分隔每个类型

```ts
let myFavoriteNumber: string | number;
myFavoriteNumber = 'seven';
myFavoriteNumber = 7;
```

```ts
let myFavoriteNumber: string | number;myFavoriteNumber = true;
// index.ts(2,1): error TS2322: Type 'boolean' is not assignable to type 'string | number'.//   Type 'boolean' is not assignable to type 'number'.
```

这里的 `let myFavoriteNumber: string | number` 的含义是，允许 `myFavoriteNumber` 的类型是 `string` 或者 `number`，但是不能是其他类型。

### 字面量联合类型

```js
let sexual: 'male' | 'female';
```

通过字面量的联合类型，来限制变量的可选值

### 访问联合类型的属性或方法

当 TypeScript 不确定一个联合类型的变量到底是哪个类型的时候，我们 **只能访问此联合类型的所有类型里共有的属性或方法**：

```ts
function getLength(something: string | number): number {
    return something.length;
}
// index.ts(2,22): error TS2339: Property 'length' does not exist on type 'string | number'.//   Property 'length' does not exist on type 'number'.

```

上例中，`length` 不是 `string` 和 `number` 的共有属性，所以会报错。

访问 `string` 和 `number` 的共有属性是没问题的：

```ts
function getString(something: string | number): string {    return something.toString();}
```

### 联合类型的类型推断

联合类型的变量在被赋值的时候，会根据类型推论的规则推断出一个类型：

```ts
let myFavoriteNumber: string | number;
myFavoriteNumber = 'seven';
console.log(myFavoriteNumber.length); 
// 5myFavoriteNumber = 7;console.log(myFavoriteNumber.length); // 编译时报错// index.ts(5,30): error TS2339: Property 'length' does not exist on type 'number'.
```

上例中，第二行的 `myFavoriteNumber` 被推断成了 `string`，访问它的 `length` 属性不会报错

而第四行的 `myFavoriteNumber` 被推断成了 `number`，访问它的 `length` 属性时就报错了

注意：联合类型发生了类型推断之后依然是联合类型

> 暂时不知道有何深意

## 对象的类型——接口

在 TypeScript 中，我们使用接口（Interfaces）来定义对象的类型

> 原生的对象有类型吗？指的是对象的构造函数？但是字面量形式声明的变量有着同一个构造函数

### Object 类型

```js
let a: object;
a = {};
a = function(){};
```

因为 js 万物皆是对象，因此这样没有什么意义，所以我们需要的是接口

我们更想要限制的是这个对象的属性

### 什么是接口

在面向对象语言中，接口（Interfaces）是一个很重要的概念，它是对行为的抽象，而具体如何行动需要由类（classes）去实现（implement）。

TypeScript 中的接口是一个非常灵活的概念，除了可用于 **对类的一部分行为进行抽象** 以外，也常用于对「对象的形状（Shape）」进行描述。

### 简单的例子

```js
let b: {name: string};
b = {name: 'str'};
```

```ts
interface Person {
    name: string;
    age: number;
    sayName(): void;
}

const tom: Person = {
    name: 'Tom',
    age: 25,
    sayName() {
        console.log(this.name);
    },
};
```

上面的例子中，我们定义了一个接口 `Person`，接着定义了一个变量 `tom`，它的类型是 `Person`。这样，我们就约束了 `tom` 的 **形状** 必须和接口 `Person` 一致。

[eslint-typescript](../engineering/eslint.md#接口名是否应该以%20I%20开头)

定义的变量比接口少了一些属性是不允许的：

```ts
interface Person {name: string; age: number;}
let tom: Person = {name: 'Tom'};
// index.ts(6,5): error TS2322: Type '{ name: string; }' is not assignable to type 'Person'.
//   Property 'age' is missing in type '{ name: string; }'.
```

类似的，多一些属性也是不允许的

可见，**赋值的时候，变量的形状必须和接口的形状保持一致**。

### 可选属性

有时我们希望不要完全匹配一个形状，那么可以用可选属性：

```ts
interface Person {    
  name: string;    
  age?: number;
}
let tom: Person = {    
  name: 'Tom'
};
let john: Person = {    
  name: 'john',    
  age: 25
};
```

可选属性的含义是该属性可以不存在。

这时 **仍然不允许添加未定义的属性**。

### 任意属性

有时候我们希望一个接口允许有任意的属性，可以使用如下方式：

```ts
interface Person {    
  name: string;    
  age?: number;    // 此处的propName仅作示意，可以是任何名字    
  [propName: string]: any;
}
let tom: Person = {    
  name: 'Tom',    
  gender: 'male'
};
```

使用 `[propName: string]` 定义了任意属性取 `string` 类型的值。

需要注意的是，**一旦定义了任意属性，那么确定属性和可选属性的类型都必须是它的类型的子集**：

```ts
interface Person {  
  name: string;  
  age?: number;  
  [propName: string]: 
  string;
}
let tom: Person = { 
  name: 'Tom', 
  age: 25,  
  gender: 'male'
};
// index.ts(3,5): error TS2411: Property 'age' of type 'number' is not assignable to string index type 'string'.
// Type '{ name: string; age: number; gender: string; }' is not assignable to type 'Person'.//   Property 'age' is incompatible with index signature.
//     Type 'number' is not assignable to type 'string'.
```

上例中，任意属性的值允许是 `string`，但是可选属性 `age` 的值却是 `number`，`number` 不是 `string` 的子属性，所以报错了。

### 只读属性

有时候我们希望对象中的一些字段只能在创建的时候被赋值，那么可以用 `readonly` 关键字定义只读属性：

```ts
interface Person {    readonly id: number;    name: string;    age?: number;    [propName: string]: any;}let tom: Person = {    id: 89757,    name: 'Tom',    gender: 'male'};tom.id = 9527;// index.ts(14,5): error TS2540: Cannot assign to 'id' because it is a constant or a read-only property.
```

上例中，使用 `readonly` 定义的属性 `id` 初始化后，又被赋值了，所以报错了。

**注意：只读的约束存在于给只读属性赋值的时候，而不是第一次给对象赋值的时候**：

```ts
interface Person {    readonly id: number;    name: string;    age?: number;    [propName: string]: any;}let tom: Person = {    name: 'Tom',    gender: 'male'};tom.id = 89757;// index.ts(8,5): error TS2322: Type '{ name: string; gender: string; }' is not assignable to type 'Person'.//   Property 'id' is missing in type '{ name: string; gender: string; }'.// index.ts(13,5): error TS2540: Cannot assign to 'id' because it is a constant or a read-only property.
```

上例中，报错信息有两处，第一处是在对 `tom` 进行赋值的时候，没有给 `id` 赋值。

第二处是在给 `tom.id` 赋值的时候，由于它是只读属性，所以报错了。

### 访问接口

接口可以通过属性名直接访问，返回值是该属性名对应的类型

```ts
interface test {    
    a: number;    
    b: string;
}

type a = test['a']; // type a= number
```

## 数组的类型

在 TypeScript 中，数组类型有多种定义方式，比较灵活。

### 「类型 + 方括号」表示法

最简单的方法是使用「类型 + 方括号」来表示数组：

```ts
let fibonacci: number[] = [1, 1, 2, 3, 5];
```

数组的项中 **不允许** 出现其他的类型：

```ts
let fibonacci: number[] = [1, '1', 2, 3, 5];
// index.ts(1,5): error TS2322: Type '(number | string)[]' is not assignable to type 'number[]'.
//   Type 'number | string' is not assignable to type 'number'.
//     Type 'string' is not assignable to type 'number'.
```

上例中，`[1, '1', 2, 3, 5]` 的类型被推断为 `(number | string)[]`，这是联合类型和数组的结合。

数组的一些方法的参数也会根据数组在定义时约定的类型进行限制：

```ts
let fibonacci: number[] = [1, 1, 2, 3, 5];
fibonacci.push('8');
// index.ts(2,16): error TS2345: Argument of type 'string' is not assignable to parameter of type 'number'
```

上例中，`push` 方法只允许传入 `number` 类型的参数，但是却传了一个 `string` 类型的参数，所以报错了。

### 数组泛型

也可以使用数组泛型（Array Generic） `Array<elemType>` 来表示数组：

```ts
let fibonacci: Array<number> = [1, 1, 2, 3, 5];
```

### 用接口表示数组

通过约束 `index` 属性，接口也可以用来描述数组：

```ts
interface NumberArray {    
    [index: number]: number;
}
let fibonacci: NumberArray = [1, 1, 2, 3, 5];
```

`NumberArray` 表示：只要 `index` 的类型是 `number`，那么值的类型必须是 `number`。

这个是又问题的，不能赋值给数组

```ts
const serializedKeyFramesData: Keyframe[] = [];

const serializedKeyFramesData: Keyframes = []; // 报错
```

用这种方法只定义了下标，没有定义 Array.prototype 上的各种方法

### Any 在数组中的应用

一个比较常见的做法是，用 `any` 表示数组中允许出现任意类型：

```ts
let list: any[] = ['Xcat Liu', 25, { website: 'http://xcatliu.com' }];
```

### 类数组

类数组（Array-like Object）不是数组类型，比如 `arguments`：

```ts
function sum() {    
	let args: number[] = arguments;
}
// index.ts(2,7): error TS2322: Type 'IArguments' is not assignable to type 'number[]'.
//   Property 'push' is missing in type 'IArguments'.
```

事实上常见的类数组都有自己的接口定义，如 `IArguments`, `NodeList`, `HTMLCollection` 等：

```ts
function sum() {    
	let args: IArguments = arguments;
}
```

更多的请参考 [内置对象](ts-basic.md#内置对象)

## 函数的类型

函数类似，我们也不想限制函数的类型为函数，而是希望限制返回值和参数类型

### 函数声明

在 JavaScript 中，有两种常见的定义函数的方式——函数声明（Function Declaration）和函数表达式（Function Expression）：

```ts
// 函数声明（Function Declaration）
function sum(x, y) {return x + y;}
// 函数表达式（Function Expression）let mySum = function (x, y) {    return x + y;};
```

一个函数有输入和输出，要在 TypeScript 中对其进行约束，需要把输入和输出都考虑到，其中函数声明的类型定义较简单：

```ts
function sum(x: number, y: number): number {return x + y;}
```

注意：**输入多余的（或者少于要求的）参数，是不被允许的**：

```ts
function sum(x: number, y: number): number {return x + y;}sum(1, 2, 3);
// index.ts(4,1): error TS2346: Supplied parameters do not match any signature of call target.
```

```ts
function sum(x: number, y: number): number {return x + y;}sum(1);
// index.ts(4,1): error TS2346: Supplied parameters do not match any signature of call target.
```

### 函数表达式

如果要我们现在写一个对函数表达式（Function Expression）的定义，可能会写成这样：

```ts
let mySum = function (x: number, y: number): number {return x + y;};
```

这是可以通过编译的，不过事实上，上面的代码只对等号右侧的匿名函数进行了类型定义，而等号左边的 `mySum`，是通过赋值操作进行类型推论而推断出来的。如果需要我们手动给 `mySum` 添加类型，则应该是这样：

```ts
let mySum: (x: number, y: number) => number = function (x: number, y: number): number {    return x + y;};
```

**注意**：不要混淆了 TypeScript 中的 `\=>` 和 ES6 中的 `\=>`

+ 在 TypeScript 的类型定义中，`\=>` 用来表示函数的定义，左边是输入类型，需要用括号括起来，右边是输出类型
+ 在 ES6 中，`\=>` 叫做箭头函数，应用十分广泛，可以参考 [ES6 中的箭头函数](http://es6.ruanyifeng.com/#docs/function#箭头函数)

> 那 typescript 中箭头函数又该如何处理呢？直接用就完事了

### 用接口定义函数的形状

我们也可以使用接口的方式来定义一个函数需要符合的形状：

```ts
interface SearchFunc {    
    (source: string, subString: string): boolean;
}
let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {    
    return source.search(subString) !== -1;
}
```

### 混合类型

有时候，一个函数还可以有自己的属性和方法：

```ts
interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}
function getCounter(): Counter {
    const counter = <Counter> function (start: number) {};
    counter.interval = 123;
    counter.reset = function () {};
    return counter;
}
const c = getCounter();
c(10);
c.reset();
c.interval = 5;
```

### 可选参数

前面提到，输入多余的（或者少于要求的）参数，是不允许的。那么如何定义可选的参数呢？

与接口中的可选属性类似，我们用 `?` 表示可选的参数：

```ts
function buildName(firstName: string, lastName?: string) {
    if (lastName) {
        return `${firstName} ${lastName}`;
    }
    return firstName;
}
const tomcat = buildName('Tom', 'Cat'); const tom = buildName('Tom');
```

需要注意的是，可选参数必须接在必需参数后面。换句话说，**可选参数后面不允许再出现必需参数了**：

```ts
function buildName(firstName?: string, lastName: string) {
    if (firstName) {
        return `${firstName} ${lastName}`;
    }
    return lastName;
}
const tomcat = buildName('Tom', 'Cat');
const tom = buildName(undefined, 'Tom');
// index.ts(1,40): error TS1016: A required parameter cannot follow an optional parameter.
```

### **参数默认值**

在 ES6 中，我们允许给函数的参数添加默认值，**TypeScript 会将添加了默认值的参数识别为可选参数**：

```ts
function buildName(firstName: string, lastName = 'Cat') {
    return `${firstName} ${lastName}`;
}
const tomcat = buildName('Tom', 'Cat');
const tom = buildName('Tom');
```

此时就不受「可选参数必须接在必需参数后面」的限制了：

```ts
function buildName(firstName = 'Tom', lastName: string) {
    return `${firstName} ${lastName}`;
}
const tomcat = buildName('Tom', 'Cat');
const cat = buildName(undefined, 'Cat');
```

> 关于默认参数，可以参考 [ES6 中函数参数的默认值](http://es6.ruanyifeng.com/#docs/function#函数参数的默认值)。

### **剩余参数**

ES6 中，可以使用 `...rest` 的方式获取函数中的剩余参数（rest 参数）：

```ts
function push(array, ...items) {
    for (const item of items) {
        array.push(item);
    }
}
const a = [];
push(a, 1, 2, 3);
```

事实上，`items` 是一个数组。所以我们可以用数组的类型来定义它：

```ts
function push(array: any[], ...items: any[]) {
    for (const item of items) {
        array.push(item);
    }
}
const a = [];
push(a, 1, 2, 3);
```

注意，rest 参数只能是最后一个参数，关于 rest 参数，可以参考 [ES6 中的 rest 参数](http://es6.ruanyifeng.com/#docs/function#rest参数)。

### 重载

重载允许 **一个函数接受不同数量或类型的参数时**，作出不同的处理。

> JS 没有真正的函数重载，因此 TS 只能实现类型的重载

比如，我们需要实现一个函数 `reverse`，输入数字 `123` 的时候，输出反转的数字 `321`，输入字符串 `'hello'` 的时候，输出反转的字符串 `'olleh'`。

利用联合类型，我们可以这么实现：

```ts
function reverse(x: number | string): number | string {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    }
    if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}
```

然而这样有一个缺点，就是不能够精确的表达，输入为数字的时候，输出也应该为数字，输入为字符串的时候，输出也应该为字符串。

这时，我们可以使用重载定义多个 `reverse` 的函数类型：

```ts
function reverse(x: number): number;
function reverse(x: string): string;
function reverse(x: number | string): number | string {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
    // 如果不写这一句话，函数默认返回undefined，但是定义的返回类型不包括undefined，因此导致编译错误
    return '123';
}
```

上例中，我们重复定义了多次函数 `reverse`，前几次都是函数定义，最后一次是函数实现。在编辑器的代码提示中，可以正确的看到前两个提示。

为了让编译器能够选择正确的检查类型，它与 JavaScript 里的处理流程相似。 它查找重载列表，尝试使用第一个重载定义。 如果匹配的话就使用这个。 因此，在定义重载的时候，一定要把最精确的定义放在最前面。

注意，`function reverse(x: number | string): number | string` 并不是重载列表的一部分，因此这里只有两个重载：一个是接收对象另一个接收数字。 以其它参数调用 `pickCard` 会产生错误。

编译的结果并不会有函数重载的效果

```js
function reverse(x) {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    }
    if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}
```

### 使用 Tuple Union 声明函数重载

适用于函数参数不同但返回值相同的场景, 比如：void 类型

```ts
function refEventEmitter(event?: string): void;
function refEventEmitter(event: string, callback: () => void): void;
function refEventEmitter(callback: () => void): void;
function refEventEmitter(
    eventOrCallback?: string | (() => void),
    callback?: () => void,
): void {
    let event: string | undefined;

    if (typeof eventOrCallback === 'function') {
        callback = eventOrCallback;
    }
    else {
        event = eventOrCallback;
    }

    // ...
}

function refEventEmitter2(
    ...args:
    | [event?: string]
    | [
        event: string,
        callback: () => unknown,
    ]
    | [callback: () => unknown]
): void {
    const [event, callback] = args.length === 2
        ? args
        : (typeof args[0] === 'function'
            ? [undefined, args[0]]
            : [args[0], undefined]);

    // ...
}
```

### 函数接口形式的重载其实意义有限

```ts
interface App {
  (args: 'aa'): number;
  (args: 'bb'): string;
}

function app_impl(args: 'aa'): number;
function app_impl(args: 'bb'): string;
function app_impl(args: 'aa'|'bb'): any {
 if (args == 'aa') return 1;
 else return 'bbbbb';
}

const app: App = app_impl;   // very ok~
```

唯一的作用就是约束，并不能起到重载的作用

# 类型断言

## 概述

类型断言（Type Assertion）可以用来手动指定一个值的类型。

```
<类型>值
```

```
值 as 类型
```

在 tsx 语法（React 的 jsx 语法的 ts 版）中必须用后一种。

## 具体用法

### 将一个联合类型的变量指定为一个更加具体的类型

当 TypeScript 不确定一个联合类型的变量到底是哪个类型的时候，我们 **只能访问此联合类型的所有类型里共有的属性或方法**：

```ts
function getLength(something: string | number): number {    return something.length;}
// index.ts(2,22): error TS2339: Property 'length' does not exist on type 'string | number'.
//   Property 'length' does not exist on type 'number'.
```

而有时候，我们确实需要在还不确定类型的时候就访问其中一个类型的属性或方法，比如：

```ts
function getLength(something: string | number): number {
	if (something.length) {
		return something.length;    
	} else {
		return something.toString().length;    
	}
}
// index.ts(2,19): error TS2339: Property 'length' does not exist on type 'string | number'.
//   Property 'length' does not exist on type 'number'.
// index.ts(3,26): error TS2339: Property 'length' does not exist on type 'string | number'.
//   Property 'length' does not exist on type 'number'.
```

上例中，获取 `something.length` 的时候会报错。

此时可以使用类型断言，将 `something` 断言成 `string`：

```tsx
function getLength(something: string | number): number {
	if ((<string>something).length) {
		return (<string>something).length;    
	} else {
		return something.toString().length;    
	}
}
```

**类型断言不是类型转换，断言成一个联合类型中不存在的类型是不允许的**：

```tsx
function toBoolean(something: string | number): boolean {
	return <boolean>something;
}
// index.ts(2,10): error TS2352: Type 'string | number' cannot be converted to type 'boolean'.
//   Type 'number' is not comparable to type 'boolean'.
```

### 将一个父类断言为更加具体的子类

当类之间有继承关系时，类型断言也是很常见的：

```ts
class ApiError extends Error {
	code: number = 0;
}
class HttpError extends Error {
    statusCode: number = 200;
}
function isApiError(error: Error) {
	if (typeof (error as ApiError).code === 'number') {
	        return true;    
	}    
	return false;
}
```

上面的例子中，我们声明了函数 `isApiError`，它用来判断传入的参数是不是 `ApiError` 类型，为了实现这样一个函数，它的参数的类型肯定得是比较抽象的父类 `Error`，这样的话这个函数就能接受 `Error` 或它的子类作为参数了。

但是由于父类 `Error` 中没有 `code` 属性，故直接获取 `error.code` 会报错，需要使用类型断言获取 `(error as ApiError).code`。

大家可能会注意到，在这个例子中有一个更合适的方式来判断是不是 `ApiError`，那就是使用 `instanceof`：

```ts
class ApiError extends Error {
  code: number = 0
}
class HttpError extends Error {
  statusCode: number = 200
}
function isApiError(error: Error) {
  if (error instanceof ApiError) {
    return true
  }
  return false
}

```

上面的例子中，确实使用 `instanceof` 更加合适，因为 `ApiError` 是一个 JavaScript 的类，能够通过 `instanceof` 来判断 `error` 是否是它的实例。

但是有的情况下 `ApiError` 和 `HttpError` 不是一个真正的类，而只是一个 TypeScript 的接口（`interface`），接口是一个类型，不是一个真正的值，它在编译结果中会被删除，当然就无法使用 `instanceof` 来做运行时判断了：

```ts
interface ApiError extends Error {
  code: number
}
interface HttpError extends Error {
  statusCode: number
}
function isApiError(error: Error) {
  if (error instanceof ApiError) {
    return true
  }
  return false
} // index.ts:9:26 - error TS2693: 'ApiError' only refers to a type, but is being used as a value here.
```

此时就只能用类型断言，通过判断是否存在 `code` 属性，来判断传入的参数是不是 `ApiError` 了：

```ts
interface ApiError extends Error {
  code: number
}
interface HttpError extends Error {
  statusCode: number
}
function isApiError(error: Error) {
  if (typeof (error as ApiError).code === "number") {
    return true
  }
  return false
}

```

### 将 `any` 断言为一个具体的类型

在日常的开发中，我们不可避免的需要处理 `any` 类型的变量，它们可能是由于第三方库未能定义好自己的类型，也有可能是历史遗留的或其他人编写的烂代码，还可能是受到 TypeScript 类型系统的限制而无法精确定义类型的场景。

遇到 `any` 类型的变量时，我们可以选择无视它，任由它滋生更多的 `any`。

我们也可以选择改进它，通过类型断言及时的把 `any` 断言为精确的类型，亡羊补牢，使我们的代码向着高可维护性的目标发展。

举例来说，历史遗留的代码中有个 `getCacheData`，它的返回值是 `any`：

```ts
function getCacheData(key: string): any {
  return (window as any).cache[key]
}
```

那么我们在使用它时，最好能够将调用了它之后的返回值断言成一个精确的类型，这样就方便了后续的操作：

```ts
function getCacheData(key: string): any {
  return (window as any).cache[key]
}
interface Cat {
  name: string
  run(): void
}
const tom = getCacheData("tom") as Cat
tom.run()
```

上面的例子中，我们调用完 `getCacheData` 之后，立即将它断言为 `Cat` 类型。这样的话明确了 `tom` 的类型，后续对 `tom` 的访问时就有了代码补全，提高了代码的可维护性。

## 类型断言的限制

从上面的例子中，我们可以总结出：

+ 联合类型可以被断言为其中一个类型
+ 父类可以被断言为子类
+ 任何类型都可以被断言为 any
+ any 可以被断言为任何类型

那么类型断言有没有什么限制呢？是不是任何一个类型都可以被断言为任何另一个类型呢？

答案是否定的——并不是任何一个类型都可以被断言为任何另一个类型。

具体来说，若 `A` 兼容 `B`，那么 `A` 能够被断言为 `B`，`B` 也能被断言为 `A`。

下面我们通过一个简化的例子，来理解类型断言的限制：

```ts
interface Animal {
  name: string
}
interface Cat {
  name: string
  run(): void
}
let tom: Cat = {
  name: "Tom",
  run: () => {
    console.log("run")
  },
}
let animal: Animal = tom
```

我们知道，TypeScript 是结构类型系统，类型之间的对比只会比较它们最终的结构，而会忽略它们定义时的关系。

在上面的例子中，`Cat` 包含了 `Animal` 中的所有属性，除此之外，它还有一个额外的方法 `run`。TypeScript 并不关心 `Cat` 和 `Animal` 之间定义时是什么关系，而只会看它们最终的结构有什么关系——所以它与 `Cat extends Animal` 是等价的：

```ts
interface Animal {
  name: string
}
interface Cat extends Animal {
  run(): void
}
```

那么也不难理解为什么 `Cat` 类型的 `tom` 可以赋值给 `Animal` 类型的 `animal` 了——就像面向对象编程中我们可以将子类的实例赋值给类型为父类的变量。

我们把它换成 TypeScript 中更专业的说法，即：`Animal` 兼容 `Cat`。

当 `Animal` 兼容 `Cat` 时，它们就可以互相进行类型断言了：

```ts
interface Animal {
  name: string
}
interface Cat {
  name: string
  run(): void
}
function testAnimal(animal: Animal) {
  return animal as Cat
}
function testCat(cat: Cat) {
  return cat as Animal
}

```

这样的设计其实也很容易就能理解：

+ 允许 `animal as Cat` 是因为「父类可以被断言为子类」，这个前面已经学习过了
+ 允许 `cat as Animal` 是因为既然子类拥有父类的属性和方法，那么被断言为父类，获取父类的属性、调用父类的方法，就不会有任何问题，故「子类可以被断言为父类」

需要注意的是，这里我们使用了简化的父类子类的关系来表达类型的兼容性，而实际上 TypeScript 在判断类型的兼容性时，比这种情况复杂很多，详细请参考类型的兼容性@link 章节。

总之，若 `A` 兼容 `B`，那么 `A` 能够被断言为 `B`，`B` 也能被断言为 `A`。

同理，若 `B` 兼容 `A`，那么 `A` 能够被断言为 `B`，`B` 也能被断言为 `A`。

所以这也可以换一种说法：

要使得 `A` 能够被断言为 `B`，只需要 `A` 兼容 `B` 或 `B` 兼容 `A` 即可，这也是为了在类型断言时的安全考虑，毕竟毫无根据的断言是非常危险的。

综上所述：

+ 联合类型可以被断言为其中一个类型
+ 父类可以被断言为子类
+ 任何类型都可以被断言为 any
+ any 可以被断言为任何类型
+ 要使得 `A` 能够被断言为 `B`，只需要 `A` 兼容 `B` 或 `B` 兼容 `A` 即可

其实前四种情况都是最后一个的特例。

## 注意

需要注意的是，类型断言只能够「欺骗」TypeScript 编译器，无法避免运行时的错误，反而滥用类型断言可能会导致运行时错误：

```ts
interface Cat {
  name: string
  run(): void
}
interface Fish {
  name: string
  swim(): void
}
function swim(animal: Cat | Fish) {
  ;(animal as Fish).swim()
}
const tom: Cat = {
  name: "Tom",
  run() {
    console.log("run")
  },
}
swim(tom) // Uncaught TypeError: animal.swim is not a function`
```

上面的例子编译时不会报错，但在运行时会报错：

```autoit
Uncaught TypeError: animal.swim is not a function`
```

原因是 `(animal as Fish).swim()` 这段代码隐藏了 `animal` 可能为 `Cat` 的情况，将 `animal` 直接断言为 `Fish` 了，而 TypeScript 编译器信任了我们的断言，故在调用 `swim()` 时没有编译错误。

可是 `swim` 函数接受的参数是 `Cat | Fish`，一旦传入的参数是 `Cat` 类型的变量，由于 `Cat` 上没有 `swim` 方法，就会导致运行时错误了。

总之，使用类型断言时一定要格外小心，尽量避免断言后调用方法或引用深层属性，以减少不必要的运行时错误。

### 双重断言

既然：

+ 任何类型都可以被断言为 any
+ any 可以被断言为任何类型

那么我们是不是可以使用双重断言 `as any as Foo` 来将任何一个类型断言为任何另一个类型呢？

```ts
interface Cat {
  run(): void
}
interface Fish {
  swim(): void
}
function testCat(cat: Cat) {
  return cat as any as Fish
}
```

在上面的例子中，若直接使用 `cat as Fish` 肯定会报错，因为 `Cat` 和 `Fish` 互相都不兼容。

但是若使用双重断言，则可以打破「要使得 `A` 能够被断言为 `B`，只需要 `A` 兼容 `B` 或 `B` 兼容 `A` 即可」的限制，将任何一个类型断言为任何另一个类型。

若你使用了这种双重断言，那么十有八九是非常错误的，它很可能会导致运行时错误。

**除非迫不得已，千万别用双重断言。**

# 内置对象

JavaScript 中有很多 [内置对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects)，它们可以直接在 TypeScript 中当做定义好了的类型。

内置对象是指根据标准在全局作用域（Global）上存在的对象。这里的标准是指 ECMAScript 和其他环境（比如 DOM）的标准。

## ECMAScript 的内置对象

ECMAScript 标准提供的内置对象有：`Boolean`、`Error`、`Date`、`RegExp` 等。

我们可以在 TypeScript 中将变量定义为这些类型：

```ts
let b: Boolean = new Boolean(1)
let e: Error = new Error("Error occurred")
let d: Date = new Date()
let r: RegExp = /[a-z]/
```

更多的内置对象，可以查看 [MDN 的文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects)。

而他们的定义文件，则在 [TypeScript 核心库的定义文件](https://github.com/Microsoft/TypeScript/tree/master/src/lib) 中。

## DOM 和 BOM 的内置对象

DOM 和 BOM 提供的内置对象有：`Document`、`HTMLElement`、`Event`、`NodeList` 等。

TypeScript 中会经常用到这些类型：

```ts
let body: HTMLElement = document.body;
let allDiv: NodeList = document.querySelectorAll('div');
document.addEventListener('click', function(e: MouseEvent) {  
	// Do something
});
```

它们的定义文件同样在 [TypeScript 核心库的定义文件](https://github.com/Microsoft/TypeScript/tree/master/src/lib) 中。

## TypeScript 核心库的定义文件

[TypeScript 核心库的定义文件](https://github.com/Microsoft/TypeScript/tree/master/src/lib) 中定义了所有浏览器环境需要用到的类型，并且是预置在 TypeScript 中的。

当你在使用一些常用的方法的时候，TypeScript 实际上已经帮你做了很多类型判断的工作了，比如：

```ts
document.addEventListener("click", function (e) {
  console.log(e.targetCurrent)
}) // index.ts(2,17): error TS2339: Property 'targetCurrent' does not exist on type 'MouseEvent'.

```

上面的例子中，`addEventListener` 方法是在 TypeScript 核心库中定义的：

```ts
interface Document
  extends Node,
    GlobalEventHandlers,
    NodeSelector,
    DocumentEvent {
  addEventListener(
    type: string,
    listener: (ev: MouseEvent) => any,
    useCapture?: boolean
  ): void
}
```

所以 `e` 被推断成了 `MouseEvent`，而 `MouseEvent` 是没有 `targetCurrent` 属性的，所以报错了。

注意，TypeScript 核心库的定义中不包含 Node.js 部分。

## 用 TypeScript 写 Node.js

Node.js 不是内置对象的一部分，如果想用 TypeScript 写 Node.js，则需要引入第三方声明文件：

```
npm install @types/node --save-dev
```

## 扩展内置对象

要么是修改内置对象的 interface

要么就是通过断言为 any

```typescript
(window as any).MyNamespace = {};
```

虽然使用 **any** 大法可以解决上述问题，但更好的方式是扩展 `lib.dom.d.ts` 文件中的 `Window` 接口来解决上述问题，具体方式如下：

```typescript
declare interface Window {
  MyNamespace: any;
}

window.MyNamespace = window.MyNamespace || {};
```

下面我们再来看一下 `lib.dom.d.ts` 文件中声明的 `Window` 接口：

```typescript
/**
 * A window containing a DOM document; the document property 
 * points to the DOM document loaded in that window. 
 */
interface Window extends EventTarget, AnimationFrameProvider, GlobalEventHandlers, 
  WindowEventHandlers, WindowLocalStorage, WindowOrWorkerGlobalScope, WindowSessionStorage {
    // 已省略大部分内容
    readonly devicePixelRatio: number;
    readonly document: Document;
    readonly top: Window;
    readonly window: Window & typeof globalThis;
    addEventListener(type: string, listener: EventListenerOrEventListenerObject, 
      options?: boolean | AddEventListenerOptions): void;
    removeEventListener<K extends keyof WindowEventMap>(type: K, 
      listener: (this: Window, ev: WindowEventMap[K]) => any, 
      options?: boolean | EventListenerOptions): void;
    [index: number]: Window;
}
```

在上面我们声明了两个相同名称的 `Window` 接口，这时并不会造成冲突。TypeScript 会自动进行接口合并，即把双方的成员放到一个同名的接口中。

# 进阶（超集）

## Type 关键字

### 类型别名

类型别名用来给一个类型起个新名字。

```ts
type Name = string;
type NameResolver = () => string;
type NameOrResolver = Name | NameResolver;
function getName(n: NameOrResolver): Name {
    if (typeof n === 'string') {
        return n;
    } else {
        return n();
    }
}
```

上例中，我们使用 `type` 创建类型别名。

类型别名常用于联合类型

### 使用 Type 定义函数类型

```ts
type test = <T>(val: T) => KeyValue[];

let test: test;

test = function <T>(val: T): KeyValue[] {
    return [{ property: 'name', value: '123' }]
}
```

**意义**: 函数的类型在函数定义的时候定义。因此使用类型别名定义的函数类型只能用于约束变量/属性，保证接收到的函数符合定义。

我另外定义了一个函数，想要使用上面的这个 type 是否可行？ 不可行，function 关键字定义的函数，类型只能在定义时决定，如下的使用没有意义

注意泛型定义的位置，是在函数类型定义处

感觉这种没有办法重载呢

## 字面量类型

### Literal Types

**字面量类型** 指的是集合类型中的更具体的子类型，而集合类型顾名思义就是某种类型的集合 (如所有的字符串都是 `string` 类型，也就是说所有的字符串都在 `string` 类型的集合中)。目前 TypeScript 支持的 3 种字面量类型有 `String Literal Types`、`Numeric Literal Types`、`Boolean Literal Types`，分别对应集合类型 `string`、`number`、`boolean`。

### 字符串字面量类型

字符串字面量类型用来约束取值只能是某几个字符串中的一个。

```ts
type EventNames = 'click' | 'scroll' | 'mousemove';
function handleEvent(ele: Element, event: EventNames) {
    // do something
}

handleEvent(document.getElementById('hello'), 'scroll');  // 没问题
handleEvent(document.getElementById('world'), 'dbclick'); // 报错，event 不能为 'dbclick'

// index.ts(7,47): error TS2345: Argument of type '"dbclick"' is not assignable to parameter of type 'EventNames'.
```

上例中，我们使用 `type` 定了一个字符串字面量类型 `EventNames`，它只能取三种字符串中的一种。

注意，**类型别名与字符串字面量类型都是使用** `type` **进行定义。**

明明不用 type 也可以定义，还是理解为类型别名就好

```js
let gender : 'male' | 'female';

gender = 'a' // Type '"a"' is not assignable to type '"male" | "female"'.
```

### 模版字面量类型

模版字面量类型以 [字符串字面量类型](https://github.com/zhongsp/TypeScript/blob/dev/zh/handbook/literal-types.md) 为基础，且可以展开为多个字符串类型的联合类型。

其语法与 [JavaScript 中的模版字面量](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals) 是一致的，但是是用在类型的位置上。 当与某个具体的字面量类型一起使用时，模版字面量会将文本连接从而生成一个新的字符串字面量类型。

```ts
type World = 'world';

type Greeting = `hello ${World}`;
//   'hello world'
```

如果在替换字符串的位置是联合类型，那么结果类型是由每个联合类型成员构成的字符串字面量的集合：

```ts
type EmailLocaleIDs = 'welcome_email' | 'email_heading';
type FooterLocaleIDs = 'footer_title' | 'footer_sendoff';

type AllLocaleIDs = `${EmailLocaleIDs | FooterLocaleIDs}_id`;
// "welcome_email_id" | "email_heading_id" | "footer_title_id" | "footer_sendoff_id"
```

多个替换字符串的位置上的多个联合类型会进行交叉相乘：

```ts
type EmailLocaleIDs = 'welcome_email' | 'email_heading';
type FooterLocaleIDs = 'footer_title' | 'footer_sendoff';

type AllLocaleIDs = `${EmailLocaleIDs | FooterLocaleIDs}_id`;
type Lang = 'en' | 'ja' | 'pt';

type LocaleMessageIDs = `${Lang}_${AllLocaleIDs}`;
//   type EmailLocaleIDs = "welcome_email" | "email_heading";
type FooterLocaleIDs = 'footer_title' | 'footer_sendoff';

type AllLocaleIDs = `${EmailLocaleIDs | FooterLocaleIDs}_id`;
type Lang = 'en' | 'ja' | 'pt';

type LocaleMessageIDs = `${Lang}_${AllLocaleIDs}`;
//   "en_welcome_email_id" | "en_email_heading_id" | "en_footer_title_id" | "en_footer_sendoff_id" | "ja_welcome_email_id" | "ja_email_heading_id" | "ja_footer_title_id" | "ja_footer_sendoff_id" | "pt_welcome_email_id" | "pt_email_heading_id" | "pt_footer_title_id" | "pt_footer_sendoff_id"
```

## 操作固有字符串的类型

为了方便字符串操作，TypeScript 提供了一系列操作字符串的类型。 这些类型内置于编译器之中，以便提高性能。 它们不存在于 TypeScript 提供的 `.d.ts` 文件中。

### `Uppercase<StringType>`

将字符串中的每个字符转换为大写字母。

```ts
type Greeting = 'Hello, world';
type ShoutyGreeting = Uppercase<Greeting>;
//   "HELLO, WORLD"

type ASCIICacheKey<Str extends string> = `ID-${Uppercase<Str>}`;
type MainID = ASCIICacheKey<'my_app'>;
//   "ID-MY_APP"
```

### `Lowercase<StringType>`

将字符串中的每个字符转换为小写字母。

```ts
type Greeting = 'Hello, world';
type QuietGreeting = Lowercase<Greeting>;
//   "hello, world"

type ASCIICacheKey<Str extends string> = `id-${Lowercase<Str>}`;
type MainID = ASCIICacheKey<'MY_APP'>;
//   "id-my_app"
```

### `Capitalize<StringType>`

将字符串中的首字母转换为大写字母。

```ts
type LowercaseGreeting = 'hello, world';
type Greeting = Capitalize<LowercaseGreeting>;
//   "Hello, world"
```

### `Uncapitalize<StringType>`

将字符串中的首字母转换为小写字母。

```ts
type UppercaseGreeting = 'HELLO WORLD';
type UncomfortableGreeting = Uncapitalize<UppercaseGreeting>;
//   "hELLO WORLD"
```

## 元组

数组合并了相同类型的对象，而元组（Tuple）合并了不同类型的对象。

元组起源于函数编程语言（如 F#），这些语言中会频繁使用元组。

元祖的元素数量是固定的

如果一个数组的元素数量是固定的，使用元祖性能会更好一点

### 简单的例子

定义一对值分别为 `string` 和 `number` 的元组：

```ts
let tom: [string, number] = ['Tom', 25];
```

当赋值或访问一个已知索引的元素时，会得到正确的类型：

```ts
let tom: [string, number];
tom[0] = 'Tom';
tom[1] = 25;

tom[0].slice(1);
tom[1].toFixed(2);
```

也可以只赋值其中一项：

```ts
let tom: [string, number];
tom[0] = 'Tom';
```

但是当直接对元组类型的变量进行初始化或者赋值的时候，需要提供所有元组类型中指定的项。

```ts
let tom: [string, number];
tom = ['Tom', 25];
```

```ts
let tom: [string, number];
tom = ['Tom'];


// Property '1' is missing in type '[string]' but required in type '[string, number]'.
```

### 越界的元素

当添加越界的元素时，它的类型会被限制为元组中每个类型的联合类型：

```ts
let tom: [string, number];
tom = ['Tom', 25];
tom.push('male');
tom.push(true);

// Argument of type 'true' is not assignable to parameter of type 'string | number'.
```

### 元祖标记

TS 4.0

下面两个函数定义在功能上是一样的：

```ts
function foo(...args: [string, number]): void {
  // ...
}

function foo(arg0: string, arg1: number): void {
  // ...
}
```

但还是有微妙的区别，下面的函数对每个参数都有名称标记，但上面通过解构定义的类型则没有，针对这种情况，Typescript 4 支持了元组标记：

```ts
type Range = [start: number, end: number];
```

同时也支持与解构一起使用：

```ts
type Foo = [first: number, second?: string, ...rest: any[]];
```

## 可变元祖类型

> TS 4.0 最重要的一个更新

考虑 `concat` 场景，接收两个数组或者元组类型，组成一个新数组：

```ts
function concat(arr1, arr2) {
  return [...arr1, ...arr2];
}
```

如果要定义 `concat` 的类型，以往我们会通过枚举的方式，先枚举第一个参数数组中的每一项：

```ts
function concat<>(arr1: [], arr2: []): [A];
function concat<A>(arr1: [A], arr2: []): [A];
function concat<A, B>(arr1: [A, B], arr2: []): [A, B];
function concat<A, B, C>(arr1: [A, B, C], arr2: []): [A, B, C];
function concat<A, B, C, D>(arr1: [A, B, C, D], arr2: []): [A, B, C, D];
function concat<A, B, C, D, E>(arr1: [A, B, C, D, E], arr2: []): [A, B, C, D, E];
function concat<A, B, C, D, E, F>(arr1: [A, B, C, D, E, F], arr2: []): [A, B, C, D, E, F];)
```

再枚举第二个参数中每一项，如果要完成所有枚举，仅考虑数组长度为 6 的情况，就要定义 36 次重载，代码几乎不可维护：

```ts
function concat<A2>(arr1: [], arr2: [A2]): [A2];
function concat<A1, A2>(arr1: [A1], arr2: [A2]): [A1, A2];
function concat<A1, B1, A2>(arr1: [A1, B1], arr2: [A2]): [A1, B1, A2];
function concat<A1, B1, C1, A2>(
  arr1: [A1, B1, C1],
  arr2: [A2]
): [A1, B1, C1, A2];
function concat<A1, B1, C1, D1, A2>(
  arr1: [A1, B1, C1, D1],
  arr2: [A2]
): [A1, B1, C1, D1, A2];
function concat<A1, B1, C1, D1, E1, A2>(
  arr1: [A1, B1, C1, D1, E1],
  arr2: [A2]
): [A1, B1, C1, D1, E1, A2];
function concat<A1, B1, C1, D1, E1, F1, A2>(
  arr1: [A1, B1, C1, D1, E1, F1],
  arr2: [A2]
): [A1, B1, C1, D1, E1, F1, A2];
```

如果我们采用批量定义的方式，问题也不会得到解决，因为参数类型的顺序得不到保证：

```ts
function concat<T, U>(arr1: T[], arr2, U[]): Array<T | U>;
```

在 Typescript 4，**可以在函数定义中对数组进行解构**，通过几行代码优雅的解决可能要重载几百次的场景：

```ts
type Arr = readonly any[];

function concat<T extends Arr, U extends Arr>(arr1: T, arr2: U): [...T, ...U] {
  return [...arr1, ...arr2];
}
```

> 用法其实有点像 infer，相当于是把 ...Arr 的结果认为是类型 T，而类型 T 是一个元祖，元祖的类型由解构的值动态决定

上面例子中，`Arr` 类型告诉 TS `T` 与 `U` 是数组类型，再通过 `[...T, ...U]` 按照逻辑顺序依次拼接类型。

再比如 `tail`，返回除第一项外剩下元素：

```ts
function tail(arg) {
  const [_, ...result] = arg;
  return result;
}
```

同样告诉 TS `T` 是数组类型，且 `arr: readonly [any, ...T]` 申明了 `T` 类型表示除第一项其余项的类型，TS 可自动将 `T` 类型关联到对象 `rest`：

```ts
function tail<T extends any[]>(arr: readonly [any, ...T]) {
  const [_ignored, ...rest] = arr;
  return rest;
}

const myTuple = [1, 2, 3, 4] as const;
const myArray = ["hello", "world"];

// type [2, 3, 4]
const r1 = tail(myTuple);

// type [2, 3, ...string[]]
const r2 = tail([...myTuple, ...myArray] as const);
```

另外之前版本的 TS 只能将类型解构放在最后一个位置：

```ts
type Strings = [string, string];
type Numbers = [number, number];

// [string, string, number, number]
type StrStrNumNum = [...Strings, ...Numbers];
```

如果你尝试将 `[...Strings, ...Numbers, boolean]` 这种写法，将会得到一个错误提示：

```ts
A rest element must be last in a tuple type.
```

但在 Typescript 4 版本支持了这种语法：

```ts
type Strings = [string, string];
type Numbers = number[];

// [string, string, ...Array<number | boolean>]
type Unbounded = [...Strings, ...Numbers, boolean];
```

### 一阶柯里化

对于再复杂一些的场景，例如高阶函数 `partialCall`，支持一定程度的柯里化：

```ts
function partialCall(f, ...headArgs) {
  return (...tailArgs) => f(...headArgs, ...tailArgs);
}
```

我们可以通过上面的特性对其进行类型定义，将函数 `f` 第一个参数类型定义为有顺序的 `[...T, ...U]`：

```ts
type Arr = readonly unknown[];

function partialCall<T extends Arr, U extends Arr, R>(
  f: (...args: [...T, ...U]) => R,
  ...headArgs: T
) {
  return (...b: U) => f(...headArgs, ...b);
}
```

测试效果如下：

```ts
const foo = (x: string, y: number, z: boolean) => {};

// This doesn't work because we're feeding in the wrong type for 'x'.
const f1 = partialCall(foo, 100);
//                          ~~~
// error! Argument of type 'number' is not assignable to parameter of type 'string'.

// This doesn't work because we're passing in too many arguments.
const f2 = partialCall(foo, "hello", 100, true, "oops");
//                                              ~~~~~~
// error! Expected 4 arguments, but got 5.

// This works! It has the type '(y: number, z: boolean) => void'
const f3 = partialCall(foo, "hello");

// What can we do with f3 now?

f3(123, true); // works!

f3();
// error! Expected 2 arguments, but got 0.

f3(123, "hello");
//      ~~~~~~~
// error! Argument of type '"hello"' is not assignable to parameter of type 'boolean'
```

值得注意的是，`const f3 = partialCall(foo, "hello");` 这段代码由于还没有执行到 `foo`，因此只匹配了第一个 `x:string` 类型，虽然后面 `y: number, z: boolean` 也是必选，但因为 `foo` 函数还未执行，此时只是参数收集阶段，因此不会报错，等到 `f3(123, true)` 执行时就会校验必选参数了，因此 `f3()` 时才会提示参数数量不正确。

### Compose

[js-functional](../../field/js-functional.md#利用可变元组)

### 枚举用法示例

https://github.com/ascoders/weekly/issues/259

```ts
const useDesigner = <T extends Arr>(
  ...selectors: T
): ReturnType<T[0]> &
  ReturnType<T[1]> &
  ReturnType<T[2]> &
  ReturnType<T[3]> => {
  return useSelector((state) =>
    selectors.reduce((selected, selector) => {
      return {
        ...selected,
        ...selector(state),
      };
    }, {})
  ) as any;
};
```

可以看到，最大的变化是不需要写四遍重载了，但由于场景和 `concat` 不同，这个例子返回值不是简单的 `[...T, ...U]`，而是 `reduce` 的结果，所以目前还只能通过枚举的方式支持。

取动态交集类型可以用 Conditional types 配合 Type inference in conditional types 的黑魔法。

```ts
type IntersectionFromUnion<TUnion> = (TUnion extends any
  ? (arg: TUnion) => void
  : never) extends (arg: infer TArg) => void
    ? TArg
    : never

const useDesigner = <T extends Array<(state?: any) => any>>(
  ...selectors: T
): IntersectionFromUnion<ReturnType<T[number]>> =>
  useSelector(state =>
    selectors.reduce((selected, selector) => {
      return {
        ...selected,
        ...selector(state)
      }
    }, {} as any)
  )
```

## 枚举

枚举（Enum）类型用于取值被限定在一定范围内的场景，比如一周只能有七天，颜色限定为红绿蓝等

> 和上面那个字符串字面量的约束有什么区别？字面量约束属于类型
>
> 而枚举是一种数据结构，常用于替换 MAP

### 纯类型不使用枚举

把 enum 摊平到 top-level。与其定义一个叫 Severity 的 enum，先对数字做 alias，例如说 type Error = 2，然后再 union 一下所有的 alias 得出 Severity。可读性跟 enum 没区别，把 alias 都 export 出去下游也能用。

有值意义的是以后可以使用枚举

### 实例

性别如果用 male 和 female 等字符串去存储非常消耗空间

```ts
enum Gender {
    male,
    female
}

let people = {
    name : '李四',
    gender : Gender.male
}
```

实际存储的时候是 0 ，但是在代码的过程中仍然逻辑清晰

> 这一个过程也和数据库非常类似，数据库中储存男女不会直接存放字符串，而是存放 0 和 1，再去匹配上另一张表的字符串

### 简单的例子

枚举使用 `enum` 关键字来定义：

```ts
enum Days {Sun, Mon, Tue, Wed, Thu, Fri, Sat};
```

枚举成员会被赋值为从 `0` 开始递增的数字，同时也会对枚举值到枚举名进行反向映射：

```ts
enum Days {Sun, Mon, Tue, Wed, Thu, Fri, Sat};


console.log(Days["Sun"] === 0); // true
console.log(Days["Mon"] === 1); // true
console.log(Days["Tue"] === 2); // true
console.log(Days["Sat"] === 6); // true


console.log(Days[0] === "Sun"); // true
console.log(Days[1] === "Mon"); // true
console.log(Days[2] === "Tue"); // true
console.log(Days[6] === "Sat"); // true
```

事实上，上面的例子会被编译为：

```ts
var Days;
(function (Days) {
    Days[Days["Sun"] = 0] = "Sun";
    Days[Days["Mon"] = 1] = "Mon";
    Days[Days["Tue"] = 2] = "Tue";
    Days[Days["Wed"] = 3] = "Wed";
    Days[Days["Thu"] = 4] = "Thu";
    Days[Days["Fri"] = 5] = "Fri";
    Days[Days["Sat"] = 6] = "Sat";
})(Days || (Days = {}));
```

### 枚举即是值也是类型

指定为枚举类型后，只能赋值枚举项目

```ts
enum groupEnum {
 muse = 'muse',
 aqours = 'aqours',
 liella = 'liella',
 nijigasaki = 'nijigasaki',
}

const a: groupEnum = groupEnum.muse;
const b = groupEnum;
```

### 手动赋值

我们也可以给枚举项手动赋值：

```ts
enum Days {Sun = 7, Mon = 1, Tue, Wed, Thu, Fri, Sat};


console.log(Days["Sun"] === 7); // true
console.log(Days["Mon"] === 1); // true
console.log(Days["Tue"] === 2); // true
console.log(Days["Sat"] === 6); // true
```

上面的例子中，未手动赋值的枚举项会接着上一个枚举项递增。

如果未手动赋值的枚举项与手动赋值的重复了，TypeScript 是不会察觉到这一点的：

```ts
enum Days {Sun = 3, Mon = 1, Tue, Wed, Thu, Fri, Sat};


console.log(Days["Sun"] === 3); // true
console.log(Days["Wed"] === 3); // true
console.log(Days[3] === "Sun"); // false
console.log(Days[3] === "Wed"); // true
```

上面的例子中，递增到 `3` 的时候与前面的 `Sun` 的取值重复了，但是 TypeScript 并没有报错，导致 `Days[3]` 的值先是 `"Sun"`，而后又被 `"Wed"` 覆盖了。编译的结果是：

```js
var Days;
(function (Days) {
    Days[Days["Sun"] = 3] = "Sun";
    Days[Days["Mon"] = 1] = "Mon";
    Days[Days["Tue"] = 2] = "Tue";
    Days[Days["Wed"] = 3] = "Wed";
    Days[Days["Thu"] = 4] = "Thu";
    Days[Days["Fri"] = 5] = "Fri";
    Days[Days["Sat"] = 6] = "Sat";
})(Days || (Days = {}));
```

所以使用的时候需要注意，最好不要出现这种覆盖的情况。

手动赋值的枚举项可以不是数字，此时需要使用类型断言来让 tsc 无视类型检查 (编译出的 js 仍然是可用的)：

```ts
enum Days {Sun = 7, Mon, Tue, Wed, Thu, Fri, Sat = <any>"S"}
```

```ts
var Days;
(function (Days) {
    Days[Days["Sun"] = 7] = "Sun";
    Days[Days["Mon"] = 8] = "Mon";
    Days[Days["Tue"] = 9] = "Tue";
    Days[Days["Wed"] = 10] = "Wed";
    Days[Days["Thu"] = 11] = "Thu";
    Days[Days["Fri"] = 12] = "Fri";
    Days[Days["Sat"] = "S"] = "Sat";
})(Days || (Days = {}));
```

当然，手动赋值的枚举项也可以为小数或负数，此时后续未手动赋值的项的递增步长仍为 `1`：

```ts
enum Days {Sun = 7, Mon = 1.5, Tue, Wed, Thu, Fri, Sat};


console.log(Days["Sun"] === 7); // true
console.log(Days["Mon"] === 1.5); // true
console.log(Days["Tue"] === 2.5); // true
console.log(Days["Sat"] === 6.5); // true
```

### 常数项和计算所得项

枚举项有两种类型：常数项（constant member）和计算所得项（computed member）。

前面我们所举的例子都是常数项，一个典型的计算所得项的例子：

```ts
enum Color {Red, Green, Blue = "blue".length};
```

上面的例子中，`"blue".length` 就是一个计算所得项。

上面的例子不会报错，但是 **如果紧接在计算所得项后面的是未手动赋值的项，那么它就会因为无法获得初始值而报错**：

```ts
enum Color {Red = "red".length, Green, Blue};

// index.ts(1,33): error TS1061: Enum member must have initializer.
// index.ts(1,40): error TS1061: Enum member must have initializer.
```

下面是常数项和计算所得项的完整定义：

+ 当满足以下条件时，枚举成员被当作是常数：
  + 不具有初始化函数并且之前的枚举成员是常数。在这种情况下，当前枚举成员的值为上一个枚举成员的值加 `1`。但第一个枚举元素是个例外。如果它没有初始化方法，那么它的初始值为 `0`。
  + 枚举成员使用常数枚举表达式初始化。常数枚举表达式是 TypeScript 表达式的子集，它可以在编译阶段求值。当一个表达式满足下面条件之一时，它就是一个常数枚举表达式：
    + 数字字面量
    + 引用之前定义的常数枚举成员（可以是在不同的枚举类型中定义的）如果这个成员是在同一个枚举类型中定义的，可以使用非限定名来引用
    + 带括号的常数枚举表达式
    + `+`, `-`, `~` 一元运算符应用于常数枚举表达式
    + `+`, `-`, `*`, `/`, `%`, `<<`, `>>`, `>>>`, `&`, `|`, `^` 二元运算符，常数枚举表达式做为其一个操作对象。若常数枚举表达式求值后为 NaN 或 Infinity，则会在编译阶段报错
+ 所有其它情况的枚举成员被当作是需要计算得出的值。

### 常数枚举

常数枚举是使用 `const enum` 定义的枚举类型：

```ts
const enum Directions {
    Up,
    Down,
    Left,
    Right
}


let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right];
```

常数枚举与普通枚举的区别是，它会在编译阶段被删除，并且不能包含计算成员。

上例的编译结果是：

```ts
var directions = [0 /* Up */, 1 /* Down */, 2 /* Left */, 3 /* Right */];
```

假如包含了计算成员，则会在编译阶段报错：

```ts
const enum Color {Red, Green, Blue = "blue".length};


// index.ts(1,38): error TS2474: In 'const' enum declarations member initializer must be constant expression.
```

### 外部枚举

外部枚举（Ambient Enums）是使用 `declare enum` 定义的枚举类型：

```ts
declare enum Directions {
    Up,
    Down,
    Left,
    Right
}


let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right];
```

之前提到过，`declare` 定义的类型只会用于编译时的检查，编译结果中会被删除。

上例的编译结果是：

```ts
var directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right];
```

外部枚举与声明语句一样，常出现在声明文件中。

同时使用 `declare` 和 `const` 也是可以的：

```ts
declare const enum Directions {
    Up,
    Down,
    Left,
    Right
}


let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right];
```

编译结果：

```ts
var directions = [0 /* Up */, 1 /* Down */, 2 /* Left */, 3 /* Right */];
```

> TypeScript 的枚举类型的概念 [来源于 C#](https://msdn.microsoft.com/zh-cn/library/sbbt4032.aspx)。

### 为数字枚举分配越界值

讲到数字枚举，这里我们再来看个问题：

```typescript
const enum Fonum {
  a = 1,
  b = 2
}

let value: Fonum = 12; // Ok
```

相信很多读者看到 `let value: Fonum = 12;` 这一行，TS 编译器并未提示任何错误会感到惊讶。很明显数字 12 并不是 Fonum 枚举的成员。 为什么会这样呢？我们来看一下 [TypeScript issues 26362](https://link.segmentfault.com/?url=https%3A%2F%2Fgithub.com%2Fmicrosoft%2FTypeScript%2Fissues%2F26362) 中 **[DanielRosenwasser](https://link.segmentfault.com/?url=https%3A%2F%2Fgithub.com%2FDanielRosenwasser)** 大佬的回答：

> The behavior is motivated by bitwise operations. There are times when SomeFlag.Foo | SomeFlag.Bar is intended to produce another SomeFlag. Instead you end up with number, and you don't want to have to cast back to SomeFlag.
>
> 该行为是由按位运算引起的。有时 SomeFlag.Foo | SomeFlag.Bar 用于生成另一个 SomeFlag。相反，你最终得到的是数字，并且你不想强制回退到 SomeFlag。

了解完上述内容，我们再来看一下 `let value: Fonum = 12;` 这个语句，该语句 TS 编译器不会报错，是因为数字 12 是可以通过 Fonum 已有的枚举成员计算而得。

```typescript
let value: Fonum = 
  Fonum.a << Fonum.b << Fonum.a |  Fonum.a << Fonum.b; // 12
```

### 数字枚举和字符串枚举

数字枚举与字符串枚举有什么区别呢？这里我们来分别看一下数字枚举和字符串枚举编译的结果：

**数字枚举编译结果**

```javascript
"use strict";
var NoYes;
(function (NoYes) {
   NoYes[NoYes["No"] = 0] = "No";
   NoYes[NoYes["Yes"] = 1] = "Yes";
})(NoYes || (NoYes = {}));
```

**字符串枚举编译结果**

```javascript
"use strict";
var NoYes;
(function (NoYes) {
   NoYes["No"] = "No";
   NoYes["Yes"] = "Yes";
})(NoYes || (NoYes = {}));
```

通过观察以上结果，我们知道数值枚举除了支持 **从成员名称到成员值** 的普通映射之外，它还支持 **从成员值到成员名称** 的反向映射。另外，对于纯字符串枚举，我们不能省略任何初始化程序。而数字枚举如果没有显式设置值时，则会使用默认值进行初始化。

### 枚举的遍历

使用 `Object.values()`，`Object.entries()`

### enumHelper

<https://github.com/zhangwilling/enumHelper>

增强了 typescript 的 enum

## Const 断言

```ts
const x = { text: "hello" } as const;
```

TypeScript 3.4 引入了一个名为 const 断言的字面值的新构造。它的语法是一个类型断言，用 const 代替类型名称（例如 `123 as const`）。当我们使用 const 断言构造新的文字表达式时，我们可以向 ts 发出以下信号：

1. 该表达式中的字面类型不应被扩展（例如：不能从“hello”转换为字符串）
2. 对象字面量获取只读属性
3. 数组文字成为只读元组

### 类型扩展

当我们使用关键字 `const` 声明一个字面量时，类型是等号右边的文字，例如：

```ts
const x = 'x'; // x has the type 'x'
```

`const` 关键字确保不会发生对变量进行重新分配，并且只保证该字面量的严格类型。

但是如果我们用 `let` 而不是 `const`， 那么该变量会被重新分配，并且类型会被扩展为字符串类型，如下所示：

```ts
let x = 'x'; // x has the type string;
```

用新的 `const` 功能，我可以这样做：

```ts
let y = 'x' as const; // y has type 'x'`
```

### 对象字面量获取只读属性

在 Typescript 3.4 之前，类型扩展发生在对象字面量中：

```ts
const action = { type: 'INCREMENT', } // has type { type: string }
```

即使我们将 `action` 声明为 `const`，仍然可以重新分配 `type` 属性，因此，该属性被扩展成了字符串类型。

如果你熟悉 [Redux](https://link.juejin.cn?target=https%3A%2F%2Fredux.js.org%2F)，就可能会发现上面的 `action` 变量可以用作 Redux action。如果你不知道 Redux 我来简单解释一下，Redux 是一个全局不可变的 state 存储。通过向所谓的 `reducers` 发送动作来修改状态。 reducers 是纯函数，它在调度每个 action 后返回全局状态的新更新版本，以反映 acion 中指定的修改。

在 Redux 中，标准做法是从名为 [action creators](https://link.juejin.cn/?target=https%3A%2F%2Fread.reduxbook.com%2Fmarkdown%2Fpart1%2F04-action-creators.html) 的函数创建操作。 action creators 只是纯函数，它返回 Redux 操作对象字面量以及提供给函数的所有参数。

用一个例子可以更好地说明这一点。应用程序可能需要一个全局 `count` 属性，为了更新这个 `count` 属性，我们可以调度类型为 `'SET_COUNT'` 的动作，它只是将全局 `count` 属性设置为一个新的值，这是一个字面对象属性。这个 action 的 action creator 将是一个函数，它接受一个数字作为参数，并返回一个具有属性为 `type`、值为 `SET_COUNT` 和类型为 `number` 的 payload 属性的对象，它将指定 `count` 的新值：

```ts
const setCount = (n: number) => {
  return {
    type: 'SET_COUNT',
    payload: n,
  }
}

const action = setCount(3)
// action has type
// { type: string, payload: number }
```

从上面显示的代码中可以看出，`type` 属性已经被扩展为 `string` 类型而不再是 `SET_COUNT`。这不是很安全的类型，我们可以保证的是 `type` 是一个字符串。 redux 中的每个 action 都有一个 `type` 属性，它是一个字符串。

这不是很好，如果我们想要利用 type 属性上的 [可区分联合](https://link.juejin.cn/?target=https%3A%2F%2Fbasarat.gitbooks.io%2Ftypescript%2Fdocs%2Ftypes%2Fdiscriminated-unions.html) 的话，那么在 TypeScript 3.4 之前，则需要为每个 action 声明一个接口或类型：

```ts
interface SetCount {
  type: 'SET_COUNT';
  payload: number;
}

const setCount = (n: number): SetCount => {
  return {
    type: 'SET_COUNT',
    payload: n,
  }
}

const action = setCount(3)
// action has type SetCount
```

这确实增加了编写 Redux action 和 reducers 的负担，但我们可以通过添加一个 `const assertion` 来解决这个问题：

```ts
const setCount = (n: number) => {
  return <const>{
    type: 'SET_COUNT',
    payload: n
  }
}

const action = setCount(3);
// action has type
//  { readonly type: "SET_COUNT"; readonly payload: number };
```

你会注意到从 `setCount` 推断的类型已经在每个属性中附加了 `readonly` 修饰符，正如文档的项目符号所述。

这就是所发生的事情：

```ts
{
  readonly type: "SET_COUNT";
  readonly payload: number
};
```

action 中的每个字面量都被添加了 `readonly` 修饰符。

在 redux 中，我们创建了一个接受 action 的联合，reducer 函数可以通过这种操作来获得良好的类型安全性。在 TypeScript 3.4 之前，我们会这样做：

```ts
interface SetCount {
  type: 'SET_COUNT';
  payload: number;
}

interface ResetCount {
  type: 'RESET_COUNT';
}

const setCount = (n: number): SetCount => {
  return {
    type: 'SET_COUNT',
    payload: n,
  }
}

const resetCount = (): ResetCount => {
  return {
    type: 'RESET_COUNT',
  }
}

type CountActions = SetCount | ResetCount
复制代码
```

我们创建了两个接口 `RESET_COUNT` 和 `SET_COUNT` 来对两个 `resetCount` 和 `setCount` 的返回类型进行归类。

`CountActions` 是这两个接口的联合。

使用 `const assertions`，我们可以通过使用 `const`、 [`ReturnType`](https://link.juejin.cn?target=https%3A%2F%2Fdev.to%2Fbusypeoples%2Fnotes-on-typescript-returntype-3m5a) 和 `typeof` 的组合来消除声明这些接口的需要：

```ts
const setCount = (n: number) => {
  return <const>{
    type: 'SET_COUNT',
    payload: n
  }
}

const resetCount = () => {
  return <const>{
    type: 'RESET_COUNT'
  }
}

type CountActions = ReturnType<typeof setCount> | ReturnType<typeof resetCount>;
复制代码
```

我们从 action creator 函数 `setCount` 和 `resetCount` 的返回类型中推断出一个很好的 action 联合。

### 数组字面量成为只读元组

在 TypeScript 3.4 之前，声明一个字面量数组将被扩展并且可以修改。

使用 const，我们可以将字面量锁定为其显式值，也不允许修改。

如果我们有一个用于设置小时数组的 redux action 类型，它可能看起来像这样：

```ts
const action = {
  type: 'SET_HOURS',
  payload: [8, 12, 5, 8],
}
//  { type: string; payload: number[]; }

action.payload.push(12) // no error
```

在 TypeScript 3.4 之前，扩展会使上述操作的字面量属性更加通用，因为它们是可以修改的。

如果我们将 `const` 应用于对象字面量，那么就可以很好地控制所有内容：

```ts
const action = <const>{
  type: 'SET_HOURS',
  payload: [8, 12, 5, 8]
}

// {
//  readonly type: "SET_HOURS";
//  readonly payload: readonly [8, 12, 5, 8];
// }

action.payload.push(12);  // error - Property 'push' does not exist on type 'readonly [8, 12, 5, 8]'.

```

这里发生的事情恰恰是文档的要点：

payload 数组确实是 [8,12,5,8] 的“只读”元组（不过我并没有从文档中看到这方面的说明）

## 类

### 相关概念

面向对象（OOP）的三大特性：封装、继承、多态

封装（Encapsulation）：将对数据的操作细节隐藏起来，只暴露对外的接口。外界调用端不需要（也不可能）知道细节，就能通过对外提供的接口来访问该对象，同时也保证了外界无法任意更改对象内部的数据

继承（Inheritance）：子类继承父类，子类除了拥有父类的所有特性外，还有一些更具体的特性

多态（Polymorphism）：由继承而产生了相关的不同的类，对同一个方法可以有不同的响应。比如 `Cat` 和 `Dog` 都继承自 `Animal`，但是分别实现了自己的 `eat` 方法。此时针对某一个实例，我们无需了解它是 `Cat` 还是 `Dog`，就可以直接调用 `eat` 方法，程序会自动判断出来应该如何执行 `eat`

存取器（getter & setter）：用以改变属性的读取和赋值行为

修饰符（Modifiers）：修饰符是一些关键字，用于限定成员或类型的性质。比如 `public` 表示公有属性或方法

抽象类（Abstract Class）：抽象类是供其他类继承的基类，抽象类不允许被实例化。抽象类中的抽象方法必须在子类中被实现

接口（Interfaces）：不同类之间公有的属性或方法，可以抽象成一个接口。接口可以被类实现（implements）。一个类只能继承自另一个类，但是可以实现多个接口

### 区别

下面举一些例子：

```ts
class Animal {
    public name;
    public constructor(name) {
        this.name = name;
    }
}

let a = new Animal('Jack');
console.log(a.name); // Jack
a.name = 'Tom';
console.log(a.name); // Tom
```

上面的例子中，`name` 被设置为了 `public`，所以直接访问实例的 `name` 属性是允许的。

**注意：**上面定义了 name 属性，但是 name 属性还没有被初始化，这个有点奇怪啊，以前好像不会这么写的 @@@ 在 ts 不写不行

![image-20210203191754145](/img/user/programming/font-end/framework/typescript/ts-basic/image-20210203191754145.png)

简便写法：

```
class Animal {
    constructor(public name: string) {
    }
}
```

### Public Private 和 Protected

TypeScript 可以使用三种访问修饰符（Access Modifiers），分别是 `public`、`private` 和 `protected`。

+ `public` 修饰的属性或方法是公有的，可以在任何地方被访问到，默认所有的属性和方法都是 `public` 的
+ `private` 修饰的属性或方法是私有的，不能在声明它的类的外部访问，子类也不能访问
+ `protected` 修饰的属性或方法是受保护的，它和 `private` 类似，区别是它在子类中也是允许被访问的

很多时候，我们希望有的属性是无法直接存取的，这时候就可以用 `private` 了：

```ts
class Animal {
  private name;
  public constructor(name) {
    this.name = name;
  }
}

let a = new Animal('Jack');
console.log(a.name); // Jack
a.name = 'Tom';

// index.ts(9,13): error TS2341: Property 'name' is private and only accessible within class 'Animal'.
// index.ts(10,1): error TS2341: Property 'name' is private and only accessible within class 'Animal'.
```

需要注意的是，TypeScript 编译之后的代码中，并没有限制 `private` 属性在外部的可访问性。

上面的例子编译后的代码是：

```ts
var Animal = (function () {
    function Animal(name) {
        this.name = name;
    }
    return Animal;
}());
var a = new Animal('Jack');
console.log(a.name);
a.name = 'Tom';
```

使用 `private` 修饰的属性或方法，在子类中也是不允许访问的：

```ts
class Animal {
  private name;
  public constructor(name) {
    this.name = name;
  }
}


class Cat extends Animal {
  constructor(name) {
    super(name);
    console.log(this.name);
  }
}


// index.ts(11,17): error TS2341: Property &#39;name&#39; is private and only accessible within class &#39;Animal&#39;.
```

而如果是用 `protected` 修饰，则允许在子类中访问：

```ts
class Animal {
    protected name;
    public constructor(name) {
        this.name = name;
    }
}

class Cat extends Animal {
    constructor(name) {
        super(name);
        console.log(this.name);
    }
}
```

当构造函数修饰为 `private` 时，该类不允许被继承或者实例化：

```ts
class Animal {
    public name;
    private constructor (name) {
        this.name = name;
  }
}
class Cat extends Animal {
    constructor (name) {
        super(name);
    }
}

let a = new Animal('Jack');

// index.ts(7,19): TS2675: Cannot extend a class 'Animal'. Class constructor is marked as private.
// index.ts(13,9): TS2673: Constructor of class 'Animal' is private and only accessible within the class declaration.
```

当构造函数修饰为 `protected` 时，该类只允许被继承：

```ts
class Animal {
    public name;
    protected constructor (name) {
        this.name = name;
  }
}
class Cat extends Animal {
    constructor (name) {
        super(name);
    }
}

let a = new Animal('Jack');

// index.ts(13,9): TS2674: Constructor of class 'Animal' is protected and only accessible within the class declaration.
```

修饰符还可以使用在 **构造函数参数** 中，等同于类中定义该属性，使代码更简洁。

```ts
class Animal {
    // public name: string;
    public constructor (public name) {
        this.name = name;
    }
}
```

### Readonly

只读属性关键字，只允许出现在属性声明或索引签名中。

```ts
class Animal {
    readonly name;
    public constructor(name) {
        this.name = name;
    }
}


let a = new Animal('Jack');
console.log(a.name); // Jack
a.name = 'Tom';


// index.ts(10,3): TS2540: Cannot assign to 'name' because it is a read-only property.
```

注意如果 `readonly` 和其他访问修饰符同时存在的话，需要写在其后面。

```ts
class Animal {
    // public readonly name;
    public constructor(public readonly name) {
        this.name = name;
    }
}
```

### 抽象类

`abstract` 用于定义抽象类和其中的抽象方法。

什么是抽象类？

首先，抽象类是不允许被实例化的：因为有一些原子性的抽象，是不希望被实例化的

```ts
abstract class Animal {
    public name;
    public constructor(name) {
        this.name = name;
    }
    public abstract sayHi();
}


let a = new Animal('Jack');


// index.ts(9,11): error TS2511: Cannot create an instance of the abstract class 'Animal'.
```

上面的例子中，我们定义了一个抽象类 `Animal`，并且定义了一个抽象方法 `sayHi`。在实例化抽象类的时候报错了。

其次，抽象类中的抽象方法必须被子类实现：

```ts
abstract class Animal {
    public name;
    public constructor(name) {
        this.name = name;
    }
    public abstract sayHi();
}

class Cat extends Animal {
    public eat() {
        console.log(`${this.name} is eating.`);
    }
}

let cat = new Cat('Tom');

// index.ts(9,7): error TS2515: Non-abstract class 'Cat' does not implement inherited abstract member 'sayHi' from class 'Animal'.
```

上面的例子中，我们定义了一个类 `Cat` 继承了抽象类 `Animal`，但是没有实现抽象方法 `sayHi`，所以编译报错了。

下面是一个正确使用抽象类的例子：

```ts
abstract class Animal {
    public name;
    public constructor(name) {
        this.name = name;
    }
    public abstract sayHi();
}


class Cat extends Animal {
    public sayHi() {
        console.log(`Meow, My name is ${this.name}`);
    }
}


let cat = new Cat('Tom');
```

上面的例子中，我们实现了抽象方法 `sayHi`，编译通过了。

需要注意的是，即使是抽象方法，TypeScript 的编译结果中，仍然会存在这个类，上面的代码的编译结果是：

```ts
var __extends = (this && this.__extends) || function (d, b) {
    for (var p in b) if (b.hasOwnProperty(p)) d[p] = b[p];
    function __() { this.constructor = d; }
    d.prototype = b === null ? Object.create(b) : (__.prototype = b.prototype, new __());
};
var Animal = (function () {
    function Animal(name) {
        this.name = name;
    }
    return Animal;
}());
var Cat = (function (_super) {
    __extends(Cat, _super);
    function Cat() {
        _super.apply(this, arguments);
    }
    Cat.prototype.sayHi = function () {
        console.log('Meow, My name is ' + this.name);
    };
    return Cat;
}(Animal));
var cat = new Cat('Tom');
```

### 类的类型

给类加上 TypeScript 的类型很简单，与接口类似：

```ts
class Animal {
    name: string;
    constructor(name: string) {
        this.name = name;
    }
    sayHi(): string {
      return `My name is ${this.name}`;
    }
}


let a: Animal = new Animal('Jack');
console.log(a.sayHi()); // My name is Jack
```

### Class 从构造函数推断成员变量类型

构造函数在类实例化时负责一些初始化工作，比如为成员变量赋值，在 Typescript 4，在构造函数里对成员变量的赋值可以直接为成员变量推导类型：

```ts
class Square {
  // Previously: implicit any!
  // Now: inferred to `number`!
  area;
  sideLength;

  constructor(sideLength: number) {
    this.sideLength = sideLength;
    this.area = sideLength ** 2;
  }
}
```

如果对成员变量赋值包含在条件语句中，还能识别出存在 `undefined` 的风险：

```ts
class Square {
  sideLength;

  constructor(sideLength: number) {
    if (Math.random()) {
      this.sideLength = sideLength;
    }
  }

  get area() {
    return this.sideLength ** 2;
    //     ~~~~~~~~~~~~~~~
    // error! Object is possibly 'undefined'.
  }
}
```

如果在其他函数中初始化，则 TS 不能自动识别，需要用 `!:` 显式申明类型：

```ts
class Square {
  // definite assignment assertion
  //        v
  sideLength!: number;
  //         ^^^^^^^^
  // type annotation

  constructor(sideLength: number) {
    this.initialize(sideLength);
  }

  initialize(sideLength: number) {
    this.sideLength = sideLength;
  }

  get area() {
    return this.sideLength ** 2;
  }
}
```

### Type Parameters in Static Members

泛型类的静态成员永远不能引用类的类型参数

因为类的范型是在实例化的时候动态定义的, 而静态成员是类自身的属性, 类型只能是固定的, 不能随着实例化动态变化

[TypeScript: Documentation - Classes](https://www.typescriptlang.org/docs/handbook/2/classes.html#type-parameters-in-static-members)

## `this`at Runtime in Classes

[TypeScript: Documentation - Classes](https://www.typescriptlang.org/docs/handbook/2/classes.html#this-at-runtime-in-classes)

```ts
// TypeScript input with 'this' parameter
function fn(this: SomeType, x: number) {
  /* ... */
}
```

## 类与接口

接口（Interfaces）可以用于对「对象的形状（Shape）」进行描述。

这一章主要介绍接口的另一个用途，对类的一部分行为进行抽象。

### 类实现接口

实现（implements）是面向对象中的一个重要概念。一般来讲，一个类只能继承自另一个类，有时候不同类之间可以有一些共有的特性，这时候就可以把特性提取成接口（interfaces），用 `implements` 关键字来实现。这个特性大大提高了面向对象的灵活性。

举例来说，门是一个类，防盗门是门的子类。如果防盗门有一个报警器的功能，我们可以简单的给防盗门添加一个报警方法。这时候如果有另一个类，车，也有报警器的功能，就可以考虑把报警器提取出来，作为一个接口，防盗门和车都去实现它：

```ts
interface Alarm {
    alert();
}

class Door {
}

class SecurityDoor extends Door implements Alarm {
    alert() {
        console.log('SecurityDoor alert');
    }
}

class Car implements Alarm {
    alert() {
        console.log('Car alert');
    }
}
```

一个类可以实现多个接口：

```ts
interface Alarm {
    alert();
}

interface Light {
    lightOn();
    lightOff();
}

class Car implements Alarm, Light {
    alert() {
        console.log('Car alert');
    }
    lightOn() {
        console.log('Car light on');
    }
    lightOff() {
        console.log('Car light off');
    }
}
```

上例中，`Car` 实现了 `Alarm` 和 `Light` 接口，既能报警，也能开关车灯。

### 行为的抽象

接口的意义并不是代码的聚合，而是类的行为的抽象，通过 `implements` 关键字，表明这些类都有着同样的行为，即是实现的接口

每个接口都需要类自己去实现，并没有代码聚合的作用

### 接口继承接口

接口与接口之间可以是继承关系：

```ts
interface Alarm {
    alert();
}


interface LightableAlarm extends Alarm {
    lightOn();
    lightOff();
}
```

上例中，我们使用 `extends` 使 `LightableAlarm` 继承 `Alarm`。

### 接口继承类

接口也可以继承类：

```ts
class Point {
    x: number;
    y: number;
}


interface Point3d extends Point {
    z: number;
}


let point3d: Point3d = {x: 1, y: 2, z: 3};
```

常见的面向对象语言中，接口是不能继承类的，但是在 TypeScript 中却是可以的

实际上，当我们在声明 `class Point` 时，除了会创建一个名为 `Point` 的类之外，同时也创建了一个名为 `Point` 的类型（实例的类型）。

所以我们既可以将 `Point` 当做一个类来用（使用 `new Point` 创建它的实例）：

也可以将 `Point` 当做一个类型来用（使用 `: Point` 表示参数的类型）：

```ts
class Point {
    x: number;
    y: number;
    constructor(x: number, y: number) {
        this.x = x;
        this.y = y;
    }
}

function printPoint(p: Point) {
    console.log(p.x, p.y);
}

printPoint(new Point(1, 2));
```

这个例子实际上可以等价于：

```ts
class Point {
    x: number;
    y: number;
    constructor(x: number, y: number) {
        this.x = x;
        this.y = y;
    }
}

interface PointInstanceType {
    x: number;
    y: number;
}

function printPoint(p: PointInstanceType) {
    console.log(p.x, p.y);
}

printPoint(new Point(1, 2));
```

上例中我们新声明的 `PointInstanceType` 类型，与声明 `class Point` 时创建的 `Point` 类型是等价的。

所以「接口继承类」和「接口继承接口」没有什么本质的区别。

值得注意的是，`PointInstanceType` 相比于 `Point`，缺少了 `constructor` 方法，这是因为声明 `Point` 类时创建的 `Point` 类型是不包含构造函数的。另外，除了构造函数是不包含的，静态属性或静态方法也是不包含的（实例的类型当然不应该包括构造函数、静态属性或静态方法）。

换句话说，声明 `Point` 类时创建的 `Point` 类型只包含其中的实例属性和实例方法。同样的，在接口继承类的时候，也只会继承它的实例属性和实例方法。

## 泛型 Generic Types

泛型（Generics）是指在定义 **函数或类** 的时候，不预先指定具体的类型，在使用该函数的时候由用户指定想要的类型

> 说白了就是有时候我们这个函数不能确定参数和返回值的类型

泛型会帮助我们捕获用户传入的参数的类型 (比如：number/string)，保存在类型变量中，之后我们就可以在函数、类、接口的 **内部** 使用这个类型

定义泛型的前提是可以接受类型，能做到这一点的有函数，类还有接口

### 简单的例子

首先，我们来实现一个函数 `createArray`，它可以创建一个指定长度的数组，同时将每一项都填充一个默认值：

```ts
function createArray(length: number, value: any): Array<any> {
    let result = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}


createArray(3, 'x'); // ['x', 'x', 'x']
```

这段代码编译不会报错，但是一个显而易见的缺陷是，它并没有准确的定义返回值的类型：`Array<any>` 允许数组的每一项都为任意类型。但是我们预期的是，数组中每一项都应该是输入的 `value` 的类型。

这时候，泛型就派上用场了：

```ts
function createArray<T>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}

// 在调用的时候指定泛型的类型
createArray<string>(3, 'x'); // ['x', 'x', 'x']
```

上例中，我们在函数名后添加了 `<T>`，其中 `T` 被称为 **类型变量** ，用来指代任意输入的类型，接着在调用的时候，可以指定它具体的类型为 `string`。在后面的输入 `value: T` 和输出 `Array<T>` 中即可使用了。

当然，也可以不手动指定，而让类型推论自动推算出来：

```ts
function createArray<T>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}


createArray(3, 'x'); // ['x', 'x', 'x']
```

### 多个类型参数

定义泛型的时候，可以一次定义多个类型参数：

```ts
function swap<T, U>(tuple: [T, U]): [U, T] {
    return [tuple[1], tuple[0]];
}


swap([7, 'seven']); // ['seven', 7]
```

上例中，我们定义了一个 `swap` 函数，用来交换输入的元组。

### 泛型变量字母的选择

T U V W 字母表顺序的类型选择 ABCD 等

T 代表 Type

K（Key）：表示对象中的键类型，或 `P` 表示“property”，两者都倾向于受 `PropertyKey` 或 `keyof T` 或 `keyof SomeInterface` 或 `keyof SomeClass` 的约束；

V（Value）：表示对象中的值类型

E（Element）：表示元素类型

A 表示“arguments”，R 表示“return”，分别对应函数签名的 rest 参数列表和返回类型，如 `(...args: A) => R`；

### 泛型约束

在函数内部使用泛型变量的时候，由于事先不知道它是哪种类型，所以不能随意的操作它的属性或方法：

```ts
function loggingIdentity<T>(arg: T): T {
    console.log(arg.length);
    return arg;
}


// index.ts(2,19): error TS2339: Property 'length' does not exist on type 'T'.
```

上例中，泛型 `T` 不一定包含属性 `length`，所以编译的时候报错了。

这时，我们可以对泛型进行约束，只允许这个函数传入那些包含 `length` 属性的变量。这就是泛型约束：

```ts
interface Lengthwise {
    length: number;
}


function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);
    return arg;
}
```

上例中，我们使用了 `extends` 约束了泛型 `T` 必须符合接口 `Lengthwise` 的形状，也就是必须包含 `length` 属性。

此时如果调用 `loggingIdentity` 的时候，传入的 `arg` 不包含 `length`，那么在编译阶段就会报错了：

```ts
interface Lengthwise {
    length: number;
}


function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);
    return arg;
}


loggingIdentity(7);


// index.ts(10,17): error TS2345: Argument of type '7' is not assignable to parameter of type 'Lengthwise'.
```

多个类型参数之间也可以互相约束：

```ts
function copyFields<T extends U, U>(target: T, source: U): T {
    for (let id in source) {
        target[id] = (<T>source)[id];
    }
    return target;
}


let x = { a: 1, b: 2, c: 3, d: 4 };


copyFields(x, { b: 10, d: 20 });
```

上例中，我们使用了两个类型参数，其中要求 `T` 继承 `U`，这样就保证了 `U` 上不会出现 `T` 中不存在的字段。

### 泛型接口

```ts
interface GenericType<T> {
    id: number;
    name: T;
}

function showType(args: GenericType<string>) {
    console.log(args);
}

showType({ id: 1, name: 'test' });
// Output: {id: 1, name: "test"}

function showTypeTwo(args: GenericType<number>) {
    console.log(args);
}

showTypeTwo({ id: 1, name: 4 });
// Output: {id: 1, name: 4}
```

在上面的栗子中，声明了一个 `GenericType` 接口，该接口接收泛型类型 `T`, 并通过类型 `T` 来约束接口内 `name` 的类型

注意：此时在使用泛型接口的时候，需要定义泛型的类型

函数一节提到过可以使用接口的方式来定义一个函数需要符合的形状，当然也可以使用含有泛型的接口来定义函数的形状：

```ts
interface CreateArrayFunc {
    <T>(length: number, value: T): Array<T>;
}


let createArray: CreateArrayFunc;
createArray = function<T>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}


createArray(3, 'x'); // ['x', 'x', 'x']
```

结合泛型接口，我们可以把泛型参数提前到接口名上：

```ts
interface CreateArrayFunc<T> {
    (length: number, value: T): Array<T>;
}


let createArray: CreateArrayFunc<any>;
createArray = function<T>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}


createArray(3, 'x'); // ['x', 'x', 'x']
```

### 泛型类

与泛型接口类似，泛型也可以用于类的类型定义中：

```ts
class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}


let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };
```

### 泛型参数的默认类型

在 TypeScript 2.3 以后，我们可以为泛型中的类型参数指定默认类型。当使用泛型时没有在代码中直接指定类型参数，从实际值参数中也无法推测出时，这个默认类型就会起作用。

```ts
function createArray<T = string>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}
```

### 泛型捕获类型的时机

```ts
interface characterInfo {
    pixivTag: string;
}

interface seiyuuInfo {
    twitterFollower: number;
}

interface RecordType2MemberInfo {
    'character': characterInfo
    'seiyuu': seiyuuInfo
}

function foo<RecordType extends keyof RecordType2MemberInfo>(type: RecordType): RecordType2MemberInfo[RecordType] {
    const recordType2MemberInfo = {
        'character': {
            pixivTag: 'honoka',
        },
        'seiyuu': {
            twitterFollower: 123,
        },
    };
    return recordType2MemberInfo[type];
}

const characterInfo = foo('character'); // 泛型 RecordType 捕获的是传入的字符串 character 的类型，这称作是类型捕获
const seiyuuInfo = foo<'character'>('seiyuu'); // 泛型 RecordType 捕获的是传入的类型 character 字符串
```

类型捕获：上面的第一种情况，是 ts 主动获取了 `'character'` 的类型

类型参数：上面的第二种情况，开发者主动传入了类型，ts 只是被动的接受传入的参数，赋值给类型变量

因为捕获类型的时机不同，即使定义了泛型也不一定需要传入，当然这是针对函数的泛型

接口和类型函数的泛型应该是一定要传入的？

接口和 type 关键字的泛型都是类型函数，先接受传入的类型，再返回新的类型，只能是类型参数

而函数的泛型既可以是类型捕获，也可以是类型参数

而且可以得知，类型捕获应该是默认行为，如果主动传递了类型就不会再去捕获了，情况二

## 类型函数

我编造的概念：接受一个类型参数，返回一个新的类型。

定义的方法有：

1. 函数泛型，定义的类型函数和函数类型声明糅合在一起
2. 接口泛型，接受一个类型参数，返回一个新的类型
3. 泛型类，本质是函数泛型
4. type 关键字定义类型函数

### Type 关键字定义类型函数

```ts
interface Todo {
  title: string
  description: string
  completed: boolean
}

interface Expected1 {
  title: string
}

interface Expected2 {
  title: string
  completed: boolean
}

type MyPick<T, K extends keyof T> = {
  [P in K]: T[P]
}

// 作为对比

interface myIPick<T, K extends keyof T> {
    [P in K]: T[P]
}
```

注意泛型的位置，是在 = 左边，移动到右边反而会报错，在我的理解里，type 一直是类型别名的用法，现在看来 type 还可以用于定义类型函数？因为上面这种形式不是接口吧

# 声明合并

如果定义了两个相同名字的函数、接口或类，那么它们会合并成一个类型

## 函数的合并

我们可以使用重载定义多个函数类型：

```ts
function reverse(x: number): number;
function reverse(x: string): string;
function reverse(x: number | string): number | string {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}
```

## 接口的合并

接口中的属性在合并时会简单的合并到一个接口中：

```ts
interface Alarm {
    price: number;
}
interface Alarm {
    weight: number;
}
```

相当于：

```ts
interface Alarm {
    price: number;
    weight: number;
}
```

注意，**合并的属性的类型必须是唯一的**：

```ts
interface Alarm {
    price: number;
}
interface Alarm {
    price: number;  // 虽然重复了，但是类型都是 `number`，所以不会报错
    weight: number;
}
```

```ts
interface Alarm {
    price: number;
}
interface Alarm {
    price: string;  // 类型不一致，会报错
    weight: number;
}


// index.ts(5,3): error TS2403: Subsequent variable declarations must have the same type.  Variable 'price' must be of type 'number', but here has type 'string'.
```

接口中方法的合并，与函数的合并一样：

```ts
interface Alarm {
    price: number;
    alert(s: string): string;
}
interface Alarm {
    weight: number;
    alert(s: string, n: number): string;
}
```

相当于：

```ts
interface Alarm {
    price: number;
    weight: number;
    alert(s: string): string;
    alert(s: string, n: number): string;
}
```

## 类的合并

类的合并与接口的合并规则一致。
