---
{"dg-publish":true,"permalink":"/programming/font-end/framework/typescript/ts-advanced/"}
---


# 类型上下文语法

## [联合类型 Union Types](ts-basic.md#联合类型%20Union%20Types)

## 交叉类型：Intersection Types

交叉类型是一种将多种类型组合为一种类型的方法。 这意味着你可以将给定的类型 A 与类型 B 或更多类型合并，并获得具有所有属性的单个类型。

```ts
interface LeftType = {
    id: number;
    left: string;
};

interface RightType = {
    id: number;
    right: string;
};

type IntersectionType = LeftType & RightType;

function showType(args: IntersectionType) {
    console.log(args);
}

showType({ id: 1, left: 'test', right: 'test' });
// Output: {id: 1, left: "test", right: "test"}
```

如你所见，`IntersectionType` 组合了两种类型 -`LeftType` 和 `RightType`，并使用 `＆` 符号形成了交叉类型。

如果多个类型有冲突，则新类型需要同时满足多个类型，如果无法满足，则新类型为 never

```ts
interface Interface1 {
    id: number;
    name: string;
}
interface Interface2 {
    name: number;
}
type IntersectionType = Interface1 & Interface2;
type test = IntersectionType['name'] // never

type UnionA = 'px' | 'em' | 'rem' | '%';
type UnionB = 'vh' | 'em' | 'rem' | 'pt';
type IntersectionUnion = UnionA & UnionB; // 'em' | 'rem'
```

## 类型引用 Typeof 运算符

TypeScript adds a `typeof` operator you can use in a **type** context to refer to the **type** of a variable or property:

TS 中的 typeof 运算符也可以作用于类型上下文中，通过 typeof 获取变量或者属性的 **类型引用**

```ts
let s = "hello";
let n: typeof s;
   
let n: string
```

## 类型索引 Keyof 运算符

`keyof` 类似于 `Object.keys` ，用于获取一个接口中 Key 的 **联合类型**

**返回值：**属性名的联合类型: string | number | symbol

**运算符对象：**接口或者是联合类型

```ts
interface Button {
    type: string
    text: string
}

type ButtonKeys = keyof Button
// 等效于
type ButtonKeys = "type" | "text"
```

还是拿之前的 Button 类来举例，Button 的 type 类型来自于另一个类 ButtonTypes，按照之前的写法，每次 ButtonTypes 更新都需要修改 Button 类，如果我们使用 `keyof` 就不会有这个烦恼。

```ts
interface ButtonStyle {
    color: string
    background: string
}
interface ButtonTypes {
    default: ButtonStyle
    primary: ButtonStyle
    danger: ButtonStyle
}

// 使用 keyof 后，ButtonTypes修改后，type 类型会自动修改 
interface Button {
    type: keyof ButtonTypes
    text: string
}
// 等价于
interface Button {
    type: 'default' | 'primary' | 'danger'
    text: string
}
```

## 类型映射 in 运算符

同时也是 jS 属性运算符，因此 TS 中用在类型身上和对象身上有不同的效果

TS 类型上下文中：`in` 关键词的作用主要是做类型的映射，遍历已有接口的属性名（key）或者是遍历联合类型。下面使用内置的泛型接口 `Readonly` 来举例

**返回值：**多个属性名 key: string | number | symbol

**运算符对象**：string | number | symbol

```ts
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};

interface Obj {
  a: string
  b: string
}

type ReadOnlyObj = Readonly<Obj>

// 等价于
type ReadOnlyObj = {
    readonly a: string
    readonly b: string
}
```

### Enum

```ts
export enum SeiyuuRecordType {
    twitterFollower = 'twitter_follower',
    youtube = 'youtube',
    ins = 'ins'
}

type test<K extends string> = {
    [P in K]: 0
}

type A = test<SeiyuuRecordType>
```

![image-20211026203451566](/img/user/programming/font-end/framework/typescript/ts-advanced/image-20211026203451566.png)

in 运算符获取的是 enum 的 value，而不是 key

所以 A 是 twitter_follower 而不是 twitterFollower

所以 Record 工具返回的是 enum 的值索引，而不是 key 索引

## 类型约束 Extends 运算符

为什么类型约束会变成泛型约束

1. 因为一个明确的类型，根本没办法约束？
2. 接受一个未知的类型，才有必要约束

泛型只能通过 extends 去约束

这里的 `extends` 关键词不同于在 class 后使用 `extends` 的继承作用，泛型内使用的主要作用是对泛型加以约束。我们用我们前面写过的 copy 方法再举个例子：

```typescript
type BaseType = string | number | boolean

// 这里表示 copy 的参数
// 只能是字符串、数字、布尔这几种基础类型
function copy<T extends BaseType>(arg: T): T {
  return arg
}
```

## 类型访问 [key]

`key` 必须是 `keyof target`，不能是值

**返回值：**联合类型

访问接口的类型

```ts
interface test {
    a: number;
    b: string;
}

// 此处的 a 是类型 'a'
type aType = test['a']; // number

interface test {
    0: number;
    1: string;
}

type aType = test[keyof test]; // number | string
```

访问数组/元祖

```ts
type testTuple = [number, string];

type typeofTuple = testTuple[number] // string | number
```

## 类型推断 Infer 运算符

### 提取

```ts
// 返回数组第一个元素的类型
type First<T extends any[]> = T extends [infer R, ...infer C] ? R : never; 

Expect<Equal<First<[3, 2, 1]>, 3>>,
Expect<Equal<First<[() => 123, { a: string }]>, () => 123>>,
Expect<Equal<First<[]>, never>>,
Expect<Equal<First<[undefined]>, undefined>>
```

```ts
type Awaited<T extends Promise<unknown>> = T extends Promise<infer R> ? R : never;
                                                             
type X = Promise<string>
type Y = Promise<{ field: number }>

type cases = [
  Expect<Equal<Awaited<X>, string>>,
  Expect<Equal<Awaited<Y>, { field: number }>>,
]
```

### 遍历 @link 类型遍历

## As Const 关键字

返回值：readonly type

```ts
const tesla = ['tesla', 'model 3', 'model X', 'model Y'] as const;

type Tesla = typeof tesla;
// readonly ["tesla", "model 3", "model X", "model Y"]
```

readonly 的对象无法再赋值：

1. 既不能复制给 tesla
2. tesla 也无法赋值给其他变量，因此所有的 extends 条件判断都会返回 false

# 类型操作

通过操作常见的类型，产生新的类型，所有的类型操作都属于高级类型

类型操作产生的类型仅限制用于 type 类型声明中，或者直接使用

type 给接口起别名，通过接口接受泛型，对传入的类型做操作，生成新的类型

## 映射类型 Mapped Types

映射类型允许你从一个旧的类型，生成一个新的类型。

请注意，前面介绍的某些高级类型也是映射类型。

如:

```ts
/*
Readonly， Partial和 Pick是同态的，但 Record不是。 因为 Record并不需要输入类型来拷贝属性，所以它不属于同态：
*/
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};
type Partial<T> = {
    [P in keyof T]?: T[P];
};
type Pick<T, K extends keyof T> = {
    [P in K]: T[P];
};

Record;
```

```ts
type StringMap<T> = {
    [P in keyof T]: string;
};

function showType(arg: StringMap<{ id: number; name: string }>) {
    console.log(arg);
}

showType({ id: 1, name: 'Test' });
// Error: Type 'number' is not assignable to type 'string'.

showType({ id: 'testId', name: 'This is a Test' });
// Output: {id: "testId", name: "This is a Test"}
```

`StringMap<>` 会将传入的任何类型转换为字符串。 就是说，如果我们在函数 `showType()` 中使用它，则接收到的参数必须是字符串 - 否则，TypeScript 将引发错误。

## 条件类型 Conditional Types

条件类型测试两种类型，然后根据该测试的结果选择其中一种。

一种由条件表达式所决定的类型， 表现形式为 `T extends U ? X : Y` ,

**如果类型 `T` 可以被赋值给类型 `U`**，那么结果类型就是 `X` 类型，否则为 `Y` 类型

> 条件类型使类型具有了不唯一性，增加了语言的灵活性

```ts
// 源码实现
type NonNullable<T> = T extends null | undefined ? never : T;

// NotNull<T> 等价于 NoneNullable<T,U>

// 用法示例
type resType = NonNullable<string | number | null | undefined>; // string|number
```

上面的代码中， `NonNullable` 检查类型是否为 `null`，并根据该类型进行处理。 正如你所看到的，它使用了 JavaScript 三元运算符。

### 示例

```ts
type If<C extends boolean, T, F> = C extends true ? T : F;

import { Equal, Expect } from '@type-challenges/utils'

type cases = [
  Expect<Equal<If<true, 'a', 'b'>, 'a'>>,
  Expect<Equal<If<false, 'a', 2>, 2>>,
]
```

### Extends 判断细节

`extends` 判断条件真假的逻辑是什么？**如果 extends 前面的类型能够赋值给 extends 后面的类型，那么表达式判断为真，否则为假**

**示例一**

```ts
interface Animal {
    eat(): void;
}

interface Dog extends Animal {
    bite(): void;
}

// A的类型为string
type A = Dog extends Animal ? string : number

const a: A = 'this is string';
```

**示例二**

```ts
// 示例2
interface A1 {
    name: string;
}

interface A2 {
    name: string;
    age: number;
}
// A的类型为string
type A = A2 extends A1 ? string : number

const a: A = 'this is string';
```

A1，A2 两个接口，满足 A2 的接口一定可以满足 A1，所以条件为真，A 的类型取 string

**示例三：泛型**

```ts
type A1 = 'x' extends 'x' ? string : number; // string
type A2 = 'x' | 'y' extends 'x' ? string : number; // number

type P<T> = T extends 'x' ? string : number;
type A3 = P<'x' | 'y'> // string | number
```

这个反直觉结果的原因就是所谓的 **分配条件类型**（Distributive Conditional Types）

> When conditional types act on a generic type, they become distributive when given a union type
>
> <https://www.typescriptlang.org/docs/handbook/2/conditional-types.html#distributive-conditional-types>

**结论：**对于使用 extends 关键字的条件类型，如果：

1. extends 前面的参数是一个泛型类型
2. 传入该参数的是联合类型

则使用分配律计算最终的结果

分配律是指，将联合类型的联合项拆成单项，分别代入条件类型，然后将每个单项代入得到的结果再联合起来，得到最终的判断结果

**示例四：never**

```ts
// never是所有类型的子类型
type A1 = never extends 'x' ? string : number; // string

type P<T> = T extends 'x' ? string : number;
type A2 = P<never> // never
```

上面的示例中，A2 和 A1 的结果竟然不一样，看起来 never 并不是一个联合类型，所以直接代入条件类型的定义即可，获取的结果应该和 A1 一直才对啊？

实际上，这里还是条件分配类型在起作用。**never 被认为是空的联合类型**，也就是说，没有联合项的联合类型，所以还是满足上面的分配律，然而因为没有联合项可以分配，所以 `P<T>` 的表达式其实根本就没有执行，所以 A2 的定义也就类似于永远没有返回的函数一样，是 never 类型的。

### 遍历联合类型 @link 类型遍历

可以通过 extends 条件类型 分配律 遍历联合类型

### 防止 Extends 判断中的分配

```ts
type P<T> = [T] extends ['x'] ? string : number;
type A1 = P<'x' | 'y'> // number
type A2 = P<never> // string
```

在条件判断类型的定义中，将泛型参数使用 `[]` 括起来，可阻断条件判断类型的分配，此时，传入参数 T 的类型将被当做一个整体，不再分配。

> Typically, distributivity is the desired behavior. To avoid that behavior, you can surround each side of the extends keyword with square brackets.
>
> 这里的方括号是元祖吗？ 联合类型的元祖是啥玩意

### Extends 泛型约束不存在分配

```ts
// 高级类型Pick的定义
type MyPick<T, K extends keyof T> = {
    [P in K]: T[P]
}

interface A {
    name: string;
    age: number;
    sex: number;
}

type A1 = MyPick<A, 'name'|'age'>
// 报错：类型“"key" | "noSuchKey"”不满足约束“keyof A”
type A2 = MyPick<A, 'name'|'noSuchKey'>
```

如果存在分配的话，是否应该只返回 name， 而不是报错呢？

我还是更愿意把 extends 理解为同一个关键字，但是不同的语义下有不同的效果，而不是把泛型约束也理解成条件类型

### Equal

方案一：参考 [issue](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2FMicrosoft%2FTypeScript%2Fissues%2F27024%23issuecomment-420227672)

```ts
type Equal<T, U> = T extends U
  ? U extends T
    ? true
    : false
  : false
```

目前该方案的唯一缺点是会将 any 类型与其它任何类型判为相等。

```ts
type T = Equal1<{x:any}, {x:number}> // T: true
```

**方案二**: 参考 [issue](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fmicrosoft%2FTypeScript%2Fissues%2F27024%23issuecomment-421529650)

```ts
export type Equal2<X, Y> =
  (<T>() => T extends X ? 1 : 2) extends
  (<U>() => U extends Y ? 1 : 2) ? true : false
```

目前该方案的唯一缺点是在对交叉类型的处理上有一点瑕疵。

```ts
type T = Equal2<{x:1} & {y:2}, {x:1, y:2}> // false
```

以上两种判断类型相等的方法见仁见智，笔者在此抛砖引玉。

## 内置映射类型

TypeScript 内部也提供了很多方便实用的工具（Utility Types），可帮助我们更轻松地操作类型。 如果要使用它们，你需要将类型传递给 `<>`

### Partial

`Partial<T>`

Partial 允许你将 `T` 类型的所有属性设为可选。 它将在每一个字段后面添加一个 `?`

**实例**

```ts
interface PartialType {
    id: number;
    firstName: string;
    lastName: string;
}

/*
等效于
interface PartialType {
  id?: number
  firstName?: string
  lastName?: string
}
*/

function showType(args: Partial<PartialType>) {
    console.log(args);
}

showType({ id: 1 });
// Output: {id: 1}

showType({ firstName: 'John', lastName: 'Doe' });
// Output: {firstName: "John", lastName: "Doe"}
```

**源码**

```ts
/**
 * Make all properties in T optional
 */
type Partial<T> = {
    [P in keyof T]?: T[P];
};
```

### Required

`Required<T>`

将某个类型里的属性全部变为必选项

**实例**

```ts
interface RequiredType {
    id: number;
    firstName?: string;
    lastName?: string;
}

function showType(args: Required<RequiredType>) {
    console.log(args);
}

showType({ id: 1, firstName: 'John', lastName: 'Doe' });
// Output: { id: 1, firstName: "John", lastName: "Doe" }

showType({ id: 1 });
// Error: Type '{ id: number: }' is missing the following properties from type 'Required<RequiredType>': firstName, lastName
```

上面的代码中，即使我们在使用接口之前先将某些属性设为可选，但 `Required` 被加入后也会使所有属性成为必选。 如果省略某些必选参数，TypeScript 将报错。

**源码**

```ts
/**
 * Make all properties in T required
 */
type Required<T> = {
    [P in keyof T]-?: T[P];
};
```

主要是因为 `-?` 映射条件的装饰符的能力，去掉了所有可选参数状态，更多的装饰符说明可以之前分享的 [TypeScript 的映射类型 Mapped types (e.g. { [P in K\]: T[P] })](https://juejin.cn/post/6844904066481389575)

### Readonly

`Readonly<T>`

会转换类型的所有属性，以使它们无法被修改

**实例**

```ts
interface ReadonlyType {
    id: number;
    name: string;
}

function showType(args: Readonly<ReadonlyType>) {
    args.id = 4;
    console.log(args);
}

showType({ id: 1, name: 'Doe' });
// Error: Cannot assign to 'id' because it is a read-only property.
```

我们使用 `Readonly` 来使 `ReadonlyType` 的属性不可被修改。 也就是说，如果你尝试为这些字段之一赋予新值，则会引发错误。

除此之外，你还可以在指定的属性前面使用关键字 `readonly` 使其无法被重新赋值

**源码**

```ts
/**
 * Make all properties in T readonly
 */
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};
```

### Pick

`Pick<T, K>`

此方法允许你从一个已存在的类型 `T` 中选择一些属性作为 `K`, 从而创建一个新类型即 抽取一个类型/接口中的一些子集作为一个新的类型

`T` 代表要抽取的对象

`K` 有一个约束: 一定是来自 `T` 所有属性字面量的联合类型

新的类型/属性一定要从 `K` 中选取

**实例**

```ts
interface PickType {
    id: number;
    firstName: string;
    lastName: string;
}

function showType(args: Pick<PickType, 'firstName' | 'lastName'>) {
    console.log(args);
}

showType({ firstName: 'John', lastName: 'Doe' });
// Output: {firstName: "John"}

showType({ id: 3 });
// Error: Object literal may only specify known properties, and 'id' does not exist in type 'Pick<PickType, "firstName" | "lastName">'
```

**源码**

```ts
/**
    源码实现
 * From T, pick a set of properties whose keys are in the union K
 */
type Pick<T, K extends keyof T> = {
    [P in K]: T[P];
};
```

### Omit

`Omit<T, K>`

`Omit` 的作用与 `Pick` 类型正好相反。 不是选择元素，而是从类型 `T` 中删除 `K` 个属性

**实例**

```ts
interface PickType {
    id: number;
    firstName: string;
    lastName: string;
}

function showType(args: Omit<PickType, 'firstName' | 'lastName'>) {
    console.log(args);
}

showType({ id: 7 });
// Output: {id: 7}

showType({ firstName: 'John' });
// Error: Object literal may only specify known properties, and 'firstName' does not exist in type 'Pick<PickType, "id">'
```

```ts
/**
 * Construct a type with the properties of T except for those in type K.
 */
type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>;
```

### Record

`Record<K,T>`

此工具可帮助你构造具有给定类型 `T` 的一组属性 `K` 的类型。将一个类型的属性映射到另一个类型的属性时，`Record` 非常方便。

**实例**

```typescript
interface EmployeeType {
    id: number;
    fullname: string;
    role: string;
}

let employees: Record<number, EmployeeType> = {
    0: { id: 1, fullname: 'John Doe', role: 'Designer' },
    1: { id: 2, fullname: 'Ibrahima Fall', role: 'Developer' },
    2: { id: 3, fullname: 'Sara Duckson', role: 'Developer' },
};
// 等价于
interface Employees {
    [number: number]: EmployeeType;
}

// 0: { id: 1, fullname: "John Doe", role: "Designer" },
// 1: { id: 2, fullname: "Ibrahima Fall", role: "Developer" },
// 2: { id: 3, fullname: "Sara Duckson", role: "Developer" }
```

`Record` 的工作方式相对简单。 在代码中，它期望一个 `number` 作为类型，这就是为什么我们将 0、1 和 2 作为 `employees` 变量的键的原因。 如果你尝试使用字符串作为属性，则会引发错误。同时属性是由 `EmployeeType` 给出的具有 ID，fullName 和 role 字段的对象。

**源码**

```ts
/**
 * Construct a type with a set of properties K of type T
 */
type Record<K extends keyof any, T> = {
    [P in K]: T;
};
```

## 扩展映射类型

扩展内置的映射类型，使支持更多场景、更丰富的能力

### Partial *

实现一个通用 `PartialReadonly2<T, K>`，它带有两种类型的参数 `T` 和 `K`。

 `K` 指定应设置为 Readonly 的 `T` 的属性集。如果未提供 `K`，则应使所有属性都变为只读，就像普通的 `Readonly<T>` 一样

本质：接口字段的交并补集处理

#### 方案一

从接口的交并补集出发，先取 keyofMap，计算出所需的集合，再从原接口中 Pick 需要的字段，最后通过 & 组合在一起

```ts
type MyExclude<T, U> = T extends U ? never : T;

type MyReadonly2<T, K extends keyof T = keyof T> = {
  [key in MyExclude<keyof T, K>]: T[key]
} & {
  readonly [key in K]: T[key]
}
```

#### 方案二

直接利用 & 覆盖

```ts
type MyReadonly2<T, K extends keyof T = keyof T> = T & {
  readonly [key in K]: T[key]
}

type cases = [
  Expect<Alike<MyReadonly2<Todo1>, Readonly<Todo1>>>,
  Expect<Alike<MyReadonly2<Todo1, 'title' | 'description'>, Expected>>,
  Expect<Alike<MyReadonly2<Todo2, 'title' | 'description'>, Expected>>,
]

interface Todo1 {
  title: string
  description?: string
  completed: boolean
}

interface Todo2 {
  readonly title: string
  description?: string
  completed: boolean
}

interface Expected {
  readonly title: string
  readonly description?: string
  completed: boolean
}
```

### DeepReadOnly

实现一个通用的 `DeepReadonly<T>`，它将对象的每个参数及其子对象递归地设为只读。

您可以假设在此挑战中我们仅处理对象。数组，函数，类等都无需考虑。但是，您仍然可以通过覆盖尽可能多的不同案例来挑战自己。

```ts
type DeepReadonly<T> = T extends Record<string, unknown>
    // 如果是对象，则递归
    ? {readonly [key in keyof T]: DeepReadonly<T[key]>}
    : T

type test = {
    a: () => 22;
    b: string;
    c: {
        d: boolean;
        e: {
            g: {
                h: {
                    i: true;
                    j: 'string';
                };
                k: 'hello';
            };
        };
    };
}

type testTwo = {
    (): string;
}

type readonlyX = DeepReadonly<test>
type readonlytestTwo = DeepReadonly<testTwo>
```

## 内置条件类型

### Extract<Type, Union>

`Extract<T, U>`

提取 `T` 中可以赋值给 `U` 的类型 -- 取交集

`Extract` 允许你通过选择两种不同类型中的共有属性来构造新的类型。 也就是从 `T` 中提取所有可分配给 `U` 的属性。

**实例**

```ts
interface FirstType {
    id: number;
    firstName: string;
    lastName: string;
}

interface SecondType {
    id: number;
    address: string;
    city: string;
}

type ExtractType = Extract<keyof FirstType, keyof SecondType>;
// Output: "id"
```

**源码**

```ts
/**
 * Extract from T those types that are assignable to U
 */
type Extract<T, U> = T extends U ? T : never;
```

与 `Exclude<T, U>` 完全相反的功能，用于提取指定的 **联合类型**，如果不存在提取类型，则返回 never。可以用在判断一个复杂的 联合类型 中是否包含指定子类型：

```ts
type T0 = Extract<'a' | 'b' | 'c', 'a'> // 'a'
type T1 = Extract<string | number | boolean, boolean> // boolean
```

### Exclude<Type, ExcludedUnion>

`Exclude<T, U>` -- 从 `T` 中剔除可以赋值给 `U` 的类型。

与 `Extract` 不同，`Exclude` 通过排除两个不同类型中已经存在的共有属性来构造新的类型。 它会从 `T` 中排除所有可分配给 `U` 的字段

```ts
interface FirstType {
    id: number;
    firstName: string;
    lastName: string;
}

interface SecondType {
    id: number;
    address: string;
    city: string;
}

type ExcludeType = Exclude<keyof FirstType, keyof SecondType>;

// Output; "firstName" | "lastName"
```

上面的代码可以看到，属性 `firstName` 和 `lastName` 在 `SecondType` 类型中不存在。 通过使用 `Extract` 关键字，我们可以获得 `T` 中存在而 `U` 中不存在的字段。

```ts
/**
 * Exclude from T those types that are assignable to U
 */
type Exclude<T, U> = T extends U ? never : T;
```

主要是基于 extends 条件类型的 [解析推迟的特性](https://juejin.cn/post/6844904066485583885#heading-4)，返回了匹配之外的所有 候选类型，配合 [never 类型](https://juejin.cn/post/6844904067152494599) 的空值特性，实现了这一高级类型。

注意：与 `Extract` 相同，操作的对象必须是联合类型才有意义。因为两个 shape 不相同的接口，必然无法赋值给对方，但是联合类型可以

> 解析延迟的特性对于联合类型有特殊操作？确实是这样

### NonNullable

`NonNullable<T>`

-- 从 `T` 中剔除 `null` 和 `undefined`

**实例**

```ts
type NonNullableType = string | number | null | undefined;

function showType(args: NonNullable<NonNullableType>) {
    console.log(args);
}

showType('test');
// Output: "test"

showType(1);
// Output: 1

showType(null);
// Error: Argument of type 'null' is not assignable to parameter of type 'string | number'.

showType(undefined);
// Error: Argument of type 'undefined' is not assignable to parameter of type 'string | number'.
```

我们将类型 `NonNullableType` 作为参数传递给 `NonNullable`，`NonNullable` 通过排除 `null` 和 `undefined` 来构造新类型。 也就是说，如果你传递可为空的值，TypeScript 将引发错误。

当你开启 [--strictNullChecks](../engineering/eslint/eslint-basic.md#--strictNullChecks) 设置后，TS 就会严格检查，只有被声明 null 后才能被赋值

**源码**

```ts
/**
 * Exclude null and undefined from T
 */
type NonNullable<T> = T extends null | undefined ? never : T;
```

### ReturnType

```ts
type ReturnType<T extends (...args: any) => any> = T extends (...args: any) => infer R ? R : any;
```

### Parameters

```ts
/**
 * Obtain the parameters of a function type in a tuple
 */
type Parameters<T extends (...args: any) => any> = T extends (...args: infer P) => any ? P : never;
```

## 实用类型

### ClassOf

有时候，我们要传入类本身，而不是类的实例

```ts
abstract class Animal extends React.PureComponent {
  /* Common methods here. */
}
class Cat extends Animal {}
class Dog extends Animal {}

// `AnimalComponent` must be a class of Animal.
const renderAnimal = (AnimalComponent: Animal) => {
  return <AnimalComponent/>; // WRONG!
}
```

上面的代码是错的，因为 `Animal` 是实例类型，不是类本身。应该

```ts
interface ClassOf<T> {
  new (...args: any[]): T;
}
const renderAnimal = (AnimalComponent: ClassOf<Animal>) => {
  return <AnimalComponent/>; // Good!
}

renderAnimal(Cat); // Good!
renderAnimal(Dog); // Good!
```

### Simplify

接着上面的例子，鼠标 Hover 到类型 `D` 上会显示

![img](/img/user/programming/font-end/framework/typescript/ts-advanced/v2-0a8459f92eb4a6ab49f23ab58eb36f1a_1440w.jpg)

这是正确的。但如果作为对外 API ，这样的提示很不友好，用户要点进类型定义，在迷宫里寻找答案。

类似情况还有：

![img](/img/user/programming/font-end/framework/typescript/ts-advanced/v2-6666722aebb9ee514d0dd746e4cbd516_1440w.jpg)

甚至一些流行库也有这样的问题。

对外暴露 API 时，最好能隐藏掉内部细节，显示为具体的类型

做法很简单，Pick 自己就可以了：

```ts
type Simplify<T> = Pick<T, keyof T>;
type E = Simplify<D>;
```

要注意的是这不适用于联合类型。

同时建议用 `/** */` 加上类型注释，参考 [第一篇](https://zhuanlan.zhihu.com/p/39620591) 的【巧用注释】。

## 互斥类型

### 互斥对象

某个大佬的总结：<https://juejin.cn/post/7017682613959655461>

1. xor 的原因和原理
2. 通过函数重载解决

### 互斥数组

使用 XOR 无法达到想要的效果，只能是使用 `Array<type>`，通过泛型定义数组类型为单一的元素

或者放到函数内部，通过函数重载解决

```ts
// 会被推断成：number[]，但是必须直接赋值
const anyArr = [1];

anyArr.push(1, '1');
```

## 类型遍历

函数 + 递归 = 遍历

> 不需要任何 for while 之类的关键字，只要有一个函数，然后通过递归，就可以实现遍历

### 遍历接口

直接使用映射类型即可

### 遍历嵌套接口

判断一下是否是 `Record<string, unknown>`，然后递归

### 遍历数组

更直接的实现

```ts
type TupleToUnion<T> = T extends any[] ? T[number] : never
```

```ts
type TraverseArray<T extends any[]> = T extends [infer R, ...infer S] 
 ? 'do somthine for R'
    : TraverseArray<C>
```

更详细的例子

```ts
type Equal<T, U> = T extends U
  ? U extends T
    ? true
    : false
  : false

type Includes<T extends readonly any[], U> = T extends [infer R, ...infer S]
  ? Equal<R, U> extends true
    ? true
    : Includes<S, U>
  : false

import { Equal, Expect } from '@type-challenges/utils'

type cases = [
  Expect<Equal<Includes<['Kars', 'Esidisi', 'Wamuu', 'Santana'], 'Kars'>, true>>,
  Expect<Equal<Includes<['Kars', 'Esidisi','Wamuu', 'Santana'], 'Dio'>, false>>,
  Expect<Equal<Includes<[1, 2, 3, 5, 6, 7], 7>, true>>,
  Expect<Equal<Includes<[1, 2, 3, 5, 6, 7], 4>, false>>,
  Expect<Equal<Includes<[1, 2, 3], 2>, true>>,
  Expect<Equal<Includes<[1, 2, 3], 1>, true>>,
  Expect<Equal<Includes<[{}], { a: 'A' }>, false>>,
  Expect<Equal<Includes<[boolean, 2, 3, 5, 6, 7], false>, false>>,
  Expect<Equal<Includes<[true, 2, 3, 5, 6, 7], boolean>, false>>,
  Expect<Equal<Includes<[false, 2, 3, 5, 6, 7], false>, true>>,
  Expect<Equal<Includes<[{ a: 'A' }], { readonly a: 'A' }>, false>>,
  Expect<Equal<Includes<[{ readonly a: 'A' }], { a: 'A' }>, false>>,
]
```

TupleToUnion

```ts
type TupleToUnion<T> = T extends [infer R, ...infer S]
    ? S['length'] extends 0
        ? R
        : TupleToUnion<R | S>
    : T

type test = TupleToUnion < [123, '456', true]>;
```

```ts
type TupleToUnion<T> = T extends Array< infer R> ? R : never;
// 相当于是
const test = [123, '456', true] as const
```

### 遍历联合类型

通过 extends 条件判断

```ts
type A1 = 'x' extends 'x' ? string : number; // string
type A2 = 'x' | 'y' extends 'x' ? string : number; // number

type P<T> = T extends 'x' ? string : number;
type A3 = P<'x' | 'y'> // string | number
```

## ThisType

ThisType 和 this 参数都是指定函数的运行时 this 指向的类型，它们的使用不同

+ this 参数是作为函数的参数定义的，它可以定义在任何函数上
+ ThisType 定义一个对象中的所有函数的 this 指向类型，它只能在对象的类型声明时使用

# 类型收窄 Narrowing

[类型收窄 Narrowing](https://www.typescriptlang.org/docs/handbook/2/narrowing.html)

typescript 能够在特定区块中保证变量属于某种确定类型。可以在此区块中放心地引用此类型的属性，或者调用此类型的方法

随着 Typescript 的更新，未来也会有更多类型收窄场景

## 类型保护 Typeof Type Guards

类型保护使你可以使用运算符检查变量或对象的类型。

> 其实就是 JS 的那些运算符，通过条件判断保证类型的正确性，在 TS 上下文中有类型保护的作用，仅此而已
>
> 奇怪的是在官网中只有 typeof 被称为类型保护，其他的都是用 narrowing 一词

```typescript
function showType(x: number | string) {
    if (typeof x === 'number') {
        return `The result is ${x + x}`;
    }
    throw new Error(`This operation can't be done on a ${typeof x}`);
}

showType("I'm not a number");
// Error: This operation can't be done on a string

showType(7);
// Output: The result is 14
```

上例代码中，有一个普通的 JavaScript 条件块，通过 `typeof` 检查接收到的参数的类型

## 控制流分析

This analysis of code based on reachability is called `control flow analysis`, and TypeScript uses this flow analysis to narrow types as it encounters type guards and assignments. When a variable is analyzed, control flow can split off and re-merge over and over again, and that variable can be observed to have a different type at each point.

### Instanceof

```ts
class Foo {
    bar() {
        return 'Hello World';
    }
}

class Bar {
    baz = '123';
}

function showType(arg: Foo | Bar) {
    if (arg instanceof Foo) {
        console.log(arg.bar());
        return arg.bar();
    }

    throw new Error('The type is not supported');
}

showType(new Foo());
// Output: Hello World

showType(new Bar());
// Error: The type is not supported
```

像前面的示例一样，这也是一个类型保护，它检查接收到的参数是否是 `Foo` 类的一部分，并对其进行处理。

### In

```ts
interface FirstType {
    x: number;
}
interface SecondType {
    y: string;
}

function showType(arg: FirstType | SecondType) {
    if ('x' in arg) {
        console.log(`The property ${arg.x} exists`);
        return `The property ${arg.x} exists`;
    }
    throw new Error('This type is not expected');
}

showType({ x: 7 });
// Output: The property 7 exists

showType({ y: 'ccc' });
// Error: This type is not expected
```

什么的栗子中，使用 `in` 检查参数对象上是否存在属性 `x`。

### Truhiness

也是类型保护的一种，避免 null undefined 系列的 error

```ts
function printAll(strs: string | string[] | null) {
    if (strs && typeof strs === 'object') {
        for (const s of strs) {
            console.log(s); // 通过 truhiness 判断，排除了 null
        }
    }
    else if (typeof strs === 'string') {
        console.log(strs);
    }
}
```

```ts
function multiplyAll(
    values: number[] | undefined,
    factor: number,
): number[] | undefined {
    if (!values) {
        return values;
    }
    return values.map((x) => x * factor);
}
```

### Equality

```ts
function printAll(strs: string | string[] | null) {
    if (strs !== null) {
        if (typeof strs === 'object') {
            for (const s of strs) {
                console.log(s);
            }
        }
        else if (typeof strs === 'string') {
            console.log(strs);
        }
    }
}
```

### Assignments

## 类型谓词 Type Predicate

### 基本示例

```ts
function isNumber(x: any): x is number {
  return typeof x === "number";
}

function isString(x: any): x is string {
  return typeof x === "string";
}
```

下面我们继续以车辆和汽车的例子为例，来创建一个自定义类型保护函数 —— `isCar`，它的具体实现如下：

```typescript
function isCar(vehicle: any): vehicle is Car {
  return (vehicle as Car).turnSteeringWheel !== undefined;
}
```

你可以传递任何值给 isCar 函数，用来判断它是不是一辆车。isCar 函数与普通函数的最大区别是，该函数的返回类型是 `vehicle is Car`，这就是我们前面所说的 " 类型谓词 "。

**在 isCar 函数的方法体中，我们不仅要检查 `vehicle` 变量是否含有 `turnSteeringWheel` 属性，而且还要告诉 TS 编译器，如果上述逻辑语句的返回结果是 true，那么当前判断的 `vehicle` 变量值的类型是 Car 类型**

特点如下：

- 返回类型谓词，如 `vehicle is Car`；
- 包含可以准确确定给定变量类型的逻辑语句，如 `(vehicle as Car).turnSteeringWheel !== undefined`

### 类

You can use `this is Type` in the return position for methods in classes and interfaces. When mixed with a type narrowing (e.g. `if` statements) the type of the target object would be narrowed to the specified `Type`.

```ts
class FileSystemObject {
    isFile(): this is FileRep {
        return this instanceof FileRep;
    }

    isDirectory(): this is Directory {
        return this instanceof Directory;
    }

    isNetworked(): this is Networked & this {
        return this.networked;
    }

    constructor(public path: string, private networked: boolean) {}
}

class FileRep extends FileSystemObject {
    constructor(path: string, public content: string) {
        super(path, false);
    }
}

class Directory extends FileSystemObject {
    children: FileSystemObject[];
}

  interface Networked {
    host: string;
  }

const fso: FileSystemObject = new FileRep('foo/bar.txt', 'foo');

if (fso.isFile()) {
    fso.content;

    const fso: FileRep;
}
else if (fso.isDirectory()) {
    fso.children;

    const fso: Directory;
}
else if (fso.isNetworked()) {
    fso.host;

    const fso: Networked & FileSystemObject;
}
```

### 配合泛型

如果你要检查的类型很多，那么为每种类型创建和维护唯一的类型保护可能会变得很繁琐。针对这个问题，我们可以利用 TypeScript 的另一个特性 —— 泛型，来解决复用问题：

```ts
function isOfType<T>(
  varToBeChecked: any,
  propertyToCheckFor: keyof T
): varToBeChecked is T {
  return (varToBeChecked as T)[propertyToCheckFor] !== undefined;
}
```

在以上代码中，我们定义了一个通用的类型保护函数，你可以在需要的时候使用它来缩窄类型。以前面自定义类型保护的例子来说，我们就可以按照以下方式来使用 `isOfType` 通用的类型保护函数：

```ts
// isCar(anotherCar) -> isOfType<Car>(vehicle, 'turnSteeringWheel')
if (isOfType<Car>(vehicle, 'turnSteeringWheel')) {
  anotherCar.turnSteeringWheel('left');
  console.log("这是一辆车");
} else {
  console.log("这不是一辆车");
}
```

有了 `isOfType` 通用的类型保护函数之后，你不必再为每个要检查的类型编写唯一的类型保护函数。而且在实际的开发过程中，只要我们合理的使用类型保护函数，就可以让我们的代码在运行时能够保证类型安全。

```ts
class Box<T> {
    value?: T;

    hasValue(): this is { value: T } {
        return this.value !== undefined;
    }
}

const box = new Box();
box.value = 'Gameboy';

box.value;

if (box.hasValue()) {
    box.value;
}
```

## Discriminated Unions

```ts
interface Shape {
    kind: 'circle' | 'square';
    radius?: number;
    sideLength?: number;
}

function getArea(shape: Shape) {
  if (shape.kind === "circle") {
    return Math.PI * shape.radius ** 2; // Object is possibly 'undefined'.
  }
}
```

better

```ts

interface Circle {
    kind: 'circle';
    radius: number;
}

interface Square {
    kind: 'square';
    sideLength: number;
}

type Shape = Circle | Square;

function getArea(shape: Shape) {
    switch (shape.kind) {
        case 'circle':
            return Math.PI * shape.radius ** 2;
        case 'square':
            return shape.sideLength ** 2;
    }
}
```

## Never

When narrowing, you can reduce the options of a union to a point where you have removed all possibilities and have nothing left. In those cases, TypeScript will use a `never` type to represent a state which shouldn’t exist.

### Exhaustiveness Checking

The `never` type is assignable to every type; however, no type is assignable to `never` (except `never` itself). This means you can use narrowing and rely on `never` turning up to do exhaustive checking in a switch statement.

For example, adding a `default` to our `getArea` function which tries to assign the shape to `never` will raise when every possible case has not been handled.

```ts
type Shape = Circle | Square;
 
function getArea(shape: Shape) {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "square":
      return shape.sideLength ** 2;
    default:
      const _exhaustiveCheck: never = shape;
      return _exhaustiveCheck;
  }
}Try
```

Adding a new member to the `Shape` union, will cause a TypeScript error:

```ts
interface Triangle {
  kind: "triangle";
  sideLength: number;
}
 
type Shape = Circle | Square | Triangle;
 
function getArea(shape: Shape) {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "square":
      return shape.sideLength ** 2;
    default:
      const _exhaustiveCheck: never = shape;Type 'Triangle' is not assignable to type 'never'.Type 'Triangle' is not assignable to type 'never'.
      return _exhaustiveCheck;
  }
}
```

# 函数式 @link Js-functional

为函数式编程常用的工具函数加上类型

## 函数组合

### Compose 实现

```ts
function compose(...funcs) {
  if (funcs.length === 0) return arg => arg
  if (funcs.length === 1) return funcs[0]
  // a是累加器，最终返回的函数
  return funcs.reduce((a, b) => (...args) => a(b(...args)))
}
// args 是 c(...args)，函数从右向左执行
// compose(a,b,c,d,e) 最终返回的 (...args) => a(b(c(d(e(...args)))))
```

### 函数重载：[pipe-ts](https://github.com/unsplash/pipe-ts/blob/master/src/index.ts)

```ts
/**
 * 函数式编程，函数组合工具，按顺序传递返回值调用函数管道
 *
 * @returns
 */
export function compose<P>(): (arg: P) => P;
export function compose<A extends unknown[], B>(ab: (this: void, ...a: A) => B): (...args: A) => B;
export function compose<A extends unknown[], B, C>(
    bc: (this: void, b: B) => C,
    ab: (this: void, ...a: A) => B,
): (...args: A) => C;
export function compose<A extends unknown[], B, C, D>(
    cd: (this: void, c: C) => D,
    bc: (this: void, b: B) => C,
    ab: (this: void, ...a: A) => B,
): (...args: A) => D;
export function compose<A extends unknown[], B, C, D, E>(
    de: (this: void, d: D) => E,
    cd: (this: void, c: C) => D,
    bc: (this: void, b: B) => C,
    ab: (this: void, ...a: A) => B,
): (...args: A) => E;
export function compose<A extends unknown[], B, C, D, E, F>(
    ef: (this: void, e: E) => F,
    de: (this: void, d: D) => E,
    cd: (this: void, c: C) => D,
    bc: (this: void, b: B) => C,
    ab: (this: void, ...a: A) => B,
): (...args: A) => F;
export function compose(...funcs: UnknownFunction[]): UnknownFunction {
    if (funcs.length === 0) return (arg) => arg;
    if (funcs.length === 1) return funcs[0];
    // a是累加器，最终返回的函数
    return funcs.reduce((a, b) => (...args) => a(b(...args)));
}
```

TypeScript’s arrays are homogeneously typed - each element of `funs` must have exactly the same type - so you can't directly express the way the types vary throughout the list in TypeScript. (The above JS works at runtime because the types are erased and data is represented uniformly.) That's why Flow's `$Compose` is a special built-in type.

> 一些比较落后的做法：https://stackoverflow.com/questions/49310886/typing-compose-function-in-typescript-flow-compose
>
> 可以参考一下里面的思想

### 利用可变元祖类型

As of Typescript 4, variadic tuple types provide a way to compose a function, whos signature is inferred from an arbitrary number of input functions.

```ts
export function pipe<P>(): (arg: P) => P;
export function pipe<T extends unknown[], V >(...funcs: readonly [
    (...args: any[]) => V,
    ...any[],
    (...args: T) => any]): (...args: T) => V
export function pipe(...funcs: UnknownFunction[]): UnknownFunction {
    if (funcs.length === 0) return (arg) => arg;
    if (funcs.length === 1) return funcs[0];
    // a是累加器，最终返回的函数
    return funcs.reduce((a, b) => (...args: unknown[]) => a(b(...args)));
}
```

问题：

1. ts 解析器无法分辨是否每个函数的输出就是下一个函数的输入
2. some confus

e.g. The following will work at compile time and at runtime

```ts
let f = (x: number) => x * x;
let g = (x: number) => `1${x}`;
let h = (x: string) => ({x: Number(x)});


let foo = pipe(f, g, h);
let bar = compose(h, g, f);

console.log(foo(2)); // => { x: 14 }
console.log(bar(2)); // => { x: 14 }
```

While this will complain at runtime, but infer the signature correctly and run

```ts
let fns = [f, g, h];
let foo2 = pipe(...fns);

console.log(foo2(2)); // => { x: 14 }
```

## 柯里化

参考：https://stackoverflow.com/questions/63903982/how-to-write-curry-and-compose-in-typescript-4

# 型变和结构化类型

https://zhuanlan.zhihu.com/p/500762226

## 类型安全和型变

TypeScript 给 JavaScript 添加了一套静态类型系统，是为了保证类型安全的，也就是保证变量只能赋同类型的值，对象只能访问它有的属性、方法。

比如 number 类型的值不能赋值给 boolean 类型的变量，Date 类型的对象就不能调用 exec 方法。

这是类型检查做的事情，遇到类型安全问题会在编译时报错。

但是这种类型安全的限制也不能太死板，有的时候需要一些变通，比如子类型是可以赋值给父类型的变量的，可以完全当成父类型来使用，也就是“型变”（类型改变）。

这种“型变”分为两种，一种是子类型可以赋值给父类型，叫做协变，**一种是父类型可以赋值给子类型，叫做逆变**。

> 主要记住逆变的概念就好了，子类型也属于父类型，这个是很自然的过程，狗也是一种动物
>
> 逆变就是动物都是狗，特别不自然，很别扭，这就是逆变，让父类型也可以赋值给子类型

## 协变 (Covariance)

其中协变是很好理解的，比如我们有两个 interface：

```ts
interface Person {
    name: string;
    age: number;
} 

interface Child {
    name: string;
    age: number;
    hobbies: string[]
}
```

这里 Child 是 Person 的子类型，更具体，那么 Child 类型的变量就可以赋值给 Person 类型。

这种子类型可以赋值给父类型的情况就叫做协变。

为什么要支持协变很容易理解：类型系统支持了父子类型，那如果子类型还不能赋值给父类型，还叫父子类型么？

所以型变是实现类型父子关系必须的，它在保证类型安全的基础上，增加了类型系统的灵活性。

## 逆变 (Contravariance)

我们有这样两个函数：

```ts
let printHobbies: (guang: Guang) => void;

printHobbies = (guang) => {
    console.log(guang.hobbies);
}

let printName: (person: Person) => void;

printName = (person) => {
    console.log(person.name);
}
```

![img](/img/user/programming/font-end/framework/typescript/ts-advanced/v2-ac1e72269861cfecb760a5bdb106fcc5_1440w.jpg)

printName 的参数 Person 不是 printHobbies 的参数 Guang 的父类型么，为啥能赋值给子类型？

因为这个函数调用的时候是按照 Guang 来约束的类型，但实际上函数只用到了父类型 Person 的属性和方法，当然不会有问题，依然是类型安全的。

这就是逆变，函数的参数有逆变的性质（而返回值是协变的，也就是子类型可以赋值给父类型）。

## 协变和逆变记忆

协变是很和谐的, 子类型肯定是父类型, 一个猫肯定是一个动物, 所以协变指的就子类型可以赋值给父类型

函数的参数是逆变的, 即如果函数内部的子类型, 只用到了父类型的字段, 那么就会认为这个参数是父类型的参数, 由实际用到的字段来确定具体的类型, 一样是类型安全的, 而且类型更加灵活, 父类型可以赋值给子类型, 这就是逆变

函数参数是逆变的, 传入的参数可以更灵活

函数返回值是协变的, 返回的参数要需要符合预期

## 双向协变

那反过来呢，如果 printHoobies 赋值给 printName 会发生什么？

因为函数声明的时候是按照 Person 来约束类型，但是调用的时候是按照 Guang 的类型来访问的属性和方法，那自然类型不安全了，所以就会报错。

但是在 ts2.x 之前支持这种赋值，也就是父类型可以赋值给子类型，子类型可以赋值给父类型，既逆变又协变，叫做“双向协变”。

但是这明显是有问题的，不能保证类型安全，所以之后 ts 加了一个编译选项 strictFunctionTypes，设置为 true 就只支持函数参数的逆变，设置为 false 则是双向协变。

## 逆变的应用

在类型编程中这种逆变性质有什么用呢？

还记得之前联合转交叉的实现么？

```ts
type UnionToIntersection<U> = 
    (U extends U ? (x: U) => unknown : never) extends (x: infer R) => unknown
        ? R
        : never
```

类型参数 U 是要转换的联合类型。

U extends U 是为了触发联合类型的 distributive 的性质，让每个类型单独传入做计算，最后合并。

利用 U 做为参数构造个函数，通过模式匹配取参数的类型。

我们通过构造了多个函数类型，然后模式提取参数类型的方式，来实现了联合转交叉，这里就是因为函数参数是逆变的，会返回联合类型的几个类型的子类型，也就是更具体的交叉类型。

## 不变

逆变和协变都是型变，是针对父子类型而言的，非父子类型自然就不会型变，也就是不变：

非父子类型之间不会发生型变，只要类型不一样就会报错：

![img](/img/user/programming/font-end/framework/typescript/ts-advanced/v2-c662cdd670da8bdfeb9795e4a8351808_1440w.jpg)

## 类型父子关系的判断

那类型之间的父子关系是怎么确定的呢，好像也没有看到 extends 的继承？

像 java 里面的类型都是通过 extends 继承的，如果 A extends B，那 A 就是 B 的子类型。这种叫做名义类型系统（nominal type）。

而 ts 里不看这个，只要结构上是一致的，那么就可以确定父子关系，这种叫做结构类型系统（structual type）。

通过结构，更具体的那个是子类型。这里的 Guang 有 Person 的所有属性，并且还多了一些属性，所以 Guang 是 Person 的子类型。

### 更具体的是子类型

注意，这里用的是更具体，而不是更多。

判断联合类型父子关系的时候， 'a' | 'b' 和 'a' | 'b' | 'c' 哪个更具体？

'a' | 'b' 更具体，所以 'a' | 'b' 是 'a' | 'b' | 'c' 的子类型。

### 结构化类型 (Structual Typing)

下述代码，即使 vector3D 不是 vector2D 的子类，仍然不会报错， 因为 Typescript 不是通过继承来实现子类型，而是通过 structual typing 来实现子类型， 即虽然 vector3D 不是 vector2D 的子类但是是其子类型。

```ts
class vector2D{
  constructor(public x:number, public y: number){
    this.x = x;
    this.y = y;
  }
}
class vector3D{
  constructor(public x:number,public y:number,public z:number){
    this.x = x;
    this.y = y;
    this.z = z;
  }
}
function calculateLength(v:vector2D){
  return Math.sqrt(v.x*v.x + v.y*v.y)
}
const point = new vector3D(0,1,2)
const dist = calculateLength(point)
```

# Ts-challenge

typescrip challenges：<https://github.com/type-challenges/type-challenges/> ing

+ 获取元祖长度：<https://github.com/type-challenges/type-challenges/blob/master/questions/18-easy-tuple-length/README.zh-CN.md>
+ promise.all：<https://github.com/type-challenges/type-challenges/blob/master/questions/20-medium-promise-all/README.md>
+ further <https://zhuanlan.zhihu.com/c_206498766>

习题的过程中定义了不少类型函数，说不定能用上

数组相关的：Concat<> Includes<> Push<> Unshift<>

## [chainable](https://github.com/type-challenges/type-challenges/blob/master/questions/12-medium-chainable-options/README.zh-CN.md#%E5%8F%AF%E4%B8%B2%E8%81%94%E6%9E%84%E9%80%A0%E5%99%A8--)

```ts
type Chainable<T extends Record<string, unknown> = Record<string, unknown>> = {
    option<K extends string, V>(
        key: K,
        value: V
    ): Chainable<T & { [key in K]: V }>;
    get(): T;
};

declare const a: Chainable;

const result = a
    .option('foo', 123)
    .option('bar', { value: 'Hello World' })
    .option('name', 'type-challenges')
    .get();
```

## 数组

### [LastItem](https://github.com/type-challenges/type-challenges/blob/master/questions/15-medium-last/README.zh-CN.md#%E6%9C%80%E5%90%8E%E4%B8%80%E4%B8%AA%E5%85%83%E7%B4%A0--)

```ts
type Last<T extends any[]> = T extends [...any[], infer U] ? U : never
```

```ts
type Last<T extends any[]> = [never, ...T][T['length']]
```

常规遍历

## 其他

<https://juejin.cn/post/6844904068096196621>

# 扩展阅读

此处记录了 [官方手册](http://www.typescriptlang.org/docs/handbook/basic-types.html)（[中文版](https://zhongsp.gitbooks.io/typescript-handbook/content/)）中包含，但是本书未涉及的概念。

我认为它们是一些不重要或者不属于 TypeScript 的概念，所以这里只给出一个简单的释义，详细内容可以点击链接深入理解。

- [Never](http://www.typescriptlang.org/docs/handbook/basic-types.html#never)（[中文版](<https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Basic> Types.html#never)）：永远不存在值的类型，一般用于错误处理函数
- [Using Class Types in Generics](http://www.typescriptlang.org/docs/handbook/generics.html#using-class-types-in-generics)（[中文版](https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Generics.html#在泛型里使用类类型)）：创建工厂函数时，需要引用构造函数的类类型
- [Best common type](http://www.typescriptlang.org/docs/handbook/type-inference.html#best-common-type)（[中文版](<https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Type> Inference.html#最佳通用类型)）：数组的类型推论
- [Contextual Type](http://www.typescriptlang.org/docs/handbook/type-inference.html#contextual-type)（[中文版](<https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Type> Inference.html#上下文类型)）：函数输入的类型推论
- [Type Compatibility](http://www.typescriptlang.org/docs/handbook/type-compatibility.html)（[中文版](<https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Type> Compatibility.html)）：允许不严格符合类型，只需要在一定规则下兼容即可
- [Advanced Types](http://www.typescriptlang.org/docs/handbook/advanced-types.html#intersection-types)（[中文版](<https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Advanced> Types.html#交叉类型（intersection-types）)）：使用 `&` 将多种类型的共有部分叠加成一种类型
- [Type Guards and Differentiating Types](http://www.typescriptlang.org/docs/handbook/advanced-types.html#type-guards-and-differentiating-types)（[中文版](<https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Advanced> Types.html#类型保护与区分类型（type-guards-and-differentiating-types）)）：联合类型在一些情况下被识别为特定的类型
- [Discriminated Unions](http://www.typescriptlang.org/docs/handbook/advanced-types.html#discriminated-unions)（[中文版](<https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Advanced> Types.html#可辨识联合（discriminated-unions）)）：使用 `|` 联合多个接口的时候，通过一个共有的属性形成可辨识联合
- [Polymorphic `this` types](http://www.typescriptlang.org/docs/handbook/advanced-types.html#polymorphic-this-types)（[中文版](<https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Advanced> Types.html#多态的 this 类型)）：父类的某个方法返回 `this`，当子类继承父类后，子类的实例调用此方法，返回的 `this` 能够被 TypeScript 正确的识别为子类的实例。

# 声明空间

在 TypeScript 里存在两种声明空间：类型声明空间与变量声明空间。下文将分别讨论这两个概念。

## 类型声明空间

类型声明空间包含用来当做类型注解的内容，例如下面的类型声明：

```ts
class Foo {}
interface Bar {}
type Bas = {};
```

你可以将 `Foo`, `Bar`, `Bas` 作为类型注解使用，示例如下：

```ts
let foo: Foo;
let bar: Bar;
let bas: Bas;
```

注意，尽管你定义了 `interface Bar`，却并不能够把它作为一个变量来使用，因为它没有定义在变量声明空间中。

```ts
interface Bar {}
const bar = Bar; // Error: "cannot find name 'Bar'"
```

出现错误提示提示： `cannot find name 'Bar'` 的原因是名称 `Bar` 并未定义在变量声明空间。这将带领我们进入下一个主题 -- 变量声明空间。

## 变量声明空间

变量声明空间包含可用作变量的内容，在上文中 `Class Foo` 提供了一个类型 `Foo` 到类型声明空间，此外它同样提供了一个变量 `Foo` 到变量声明空间，如下所示：

```ts
class Foo {}
const someVar = Foo;
const someOtherVar = 123;
```

这很棒，尤其是当你想把一个类来当做变量传递时。

WARNING

我们并不能把一些如 `interface` 定义的内容当作变量使用。

与此相似，一些用 `var` 声明的变量，也只能在变量声明空间使用，不能用作类型注解。

```js
const foo = 123;
let bar: foo; // ERROR: "cannot find name 'foo'"
```

提示 `ERROR: "cannot find name 'foo'"` 原因是，名称 foo 没有定义在类型声明空间里。

# FAQ

## 类型断言 Vs 类型声明

在这个例子中：

```ts
function getCacheData(key: string): any {
    return (window as any).cache[key];
}

interface Cat {
    name: string;
    run(): void;
}

const tom = getCacheData('tom') as Cat;
tom.run();
```

我们使用 `as Cat` 将 `any` 类型断言为了 `Cat` 类型。

但实际上还有其他方式可以解决这个问题：

```ts
function getCacheData(key: string): any {
    return (window as any).cache[key];
}

interface Cat {
    name: string;
    run(): void;
}

const tom: Cat = getCacheData('tom');
tom.run();
```

上面的例子中，我们通过类型声明的方式，将 `tom` 声明为 `Cat`，然后再将 `any` 类型的 `getCacheData('tom')` 赋值给 `Cat` 类型的 `tom`。

这和类型断言是非常相似的，而且产生的结果也几乎是一样的——`tom` 在接下来的代码中都变成了 `Cat` 类型。

它们的区别，可以通过这个例子来理解：

```ts
interface Animal {
    name: string;
}
interface Cat {
    name: string;
    run(): void;
}

const animal: Animal = {
    name: 'tom'
};
let tom = animal as Cat;
```

在上面的例子中，由于 `Animal` 兼容 `Cat`，故可以将 `animal` 断言为 `Cat` 赋值给 `tom`。

但是若直接声明 `tom` 为 `Cat` 类型：

```ts
interface Animal {
    name: string;
}
interface Cat {
    name: string;
    run(): void;
}

const animal: Animal = {
    name: 'tom'
};
let tom: Cat = animal;

// index.ts:12:5 - error TS2741: Property 'run' is missing in type 'Animal' but required in type 'Cat'.
```

则会报错，不允许将 `animal` 赋值为 `Cat` 类型的 `tom`。

这很容易理解，`Animal` 可以看作是 `Cat` 的父类，当然不能将父类的实例赋值给类型为子类的变量。

深入的讲，它们的核心区别就在于：

- `animal` 断言为 `Cat`，只需要满足 `Animal` 兼容 `Cat` 或 `Cat` 兼容 `Animal` 即可
- `animal` 赋值给 `tom`，需要满足 `Cat` 兼容 `Animal` 才行

但是 `Cat` 并不兼容 `Animal`。

而在前一个例子中，由于 `getCacheData('tom')` 是 `any` 类型，`any` 兼容 `Cat`，`Cat` 也兼容 `any`，故

```ts
const tom = getCacheData('tom') as Cat;
```

等价于

```ts
const tom: Cat = getCacheData('tom');
```

知道了它们的核心区别，就知道了类型声明是比类型断言更加严格的。

所以为了增加代码的质量，我们最好优先使用类型声明，这也比类型断言的 `as` 语法更加优雅。

## 类型断言 Vs 泛型

还是这个例子：

```ts
function getCacheData(key: string): any {
    return (window as any).cache[key];
}

interface Cat {
    name: string;
    run(): void;
}

const tom = getCacheData('tom') as Cat;
tom.run();
```

我们还有第三种方式可以解决这个问题，那就是泛型：

```ts
function getCacheData<T>(key: string): T {
    return (window as any).cache[key];
}

interface Cat {
    name: string;
    run(): void;
}

const tom = getCacheData<Cat>('tom');
tom.run();
```

通过给 `getCacheData` 函数添加了一个泛型 `<T>`，我们可以更加规范的实现对 `getCacheData` 返回值的约束，这也同时去除掉了代码中的 `any`，是最优的一个解决方案。

## JS 运算符 交集

### In 运算符

JS 中如果在对象或其原型链中含有该属性，返回值是布尔类型 true or false

在 TS 类型上下文中作用是类型映射，返回值是多个属性名的联合类型

### Typeof 运算符

JS 中返回值是类型字符串

在 TS 类型上下文中作用是类型引用，返回值操作目标的类型

### Extends 运算符

类型约束、泛型约束、接口继承，不同上下文返回值是不同的

#### 条件类型里的 Extends

- 条件语句 T extends K ? : ; 是一个整体，整体类似于三目运算符， 此时 extends 返回值是一个布尔值，表明 T 类型 是否是 K 类型的子集
- 处于 TS 类型上下文，且必须是条件类型语句

#### 继承中的 Extends

- 类似于类的继承，处于 JS 上下文

#### 类型约束中的 Extends

- 约束泛型的类型，必须是子集
- 处于 TS 类型上下文，且必须是泛型约束

## Type 关键字和 Interface 关键字

1. 类型只能声明一个
2. 接口可以重复声明，重名的接口自然取并集，此外，接口和类型别名不是互斥的。接口可以扩展类型别名，而反过来是不行的。
3. 接口中的所有属性都不能有实际的值，只能定义结构，这点是和抽象类不同的，抽象类可以有实际的值和方法
4. 类继承类，类实现一个接口，使得我们的类满足 interface 的要求
5. 类实现接口，用的是 implements

### Extend

接口和类型别名都能够被扩展，但语法有所不同。

**Interface extends interface**

```typescript
interface PartialPointX { x: number; }
interface Point extends PartialPointX { 
  y: number; 
}
```

**Type alias extends type alias**

```typescript
type PartialPointX = { x: number; };
type Point = PartialPointX & { y: number; };
```

**Interface extends type alias**

```typescript
type PartialPointX = { x: number; };
interface Point extends PartialPointX { y: number; }
```

**Type alias extends interface**

```typescript
interface PartialPointX { x: number; }
type Point = PartialPointX & { y: number; };
```

## Const Assertions Vs Enum

```ts
enum BasicType {
    character = 'character',
    seiyuu = 'seiyuu',
    couple = 'couple'
}

type keyofBasicType = keyof typeof BasicType;
```

keyof 只能作用于 接口 或者联合类型，所以需要先通过 typeof 获取 enum 类型的 接口

enum 可以理解为 plain object，遍历的时候直接使用 Object.entries 即可

想把对象的键/值拿出来做枚举？可以呀：

```typescript
const Permission = {
  Read: "r",
  Write: "w",
  Execute: "x",
} as const;
type PermissionKey = keyof typeof Permission;
type PermissionValue = typeof Permission[PermissionKey];
```

看到没，PermissionKey 的类型就是 `'read' | 'write' | 'execute'` ，PermissionValue 的类型就是 `'r' | 'w' | 'x'` 了呀，而且我们可以轻松遍历 permissions 对象：

```typescript
for (const permission of Object.keys(Permission)) {
  // 对 key 为所欲为
}
for (const permission of Object.values(Permission)) {
  // 对 value 为所欲为
}
```

### Map 形式和类型函数形式

```ts
// TODO: 的区别
export type MemberInfoTypeMap = {
    [BasicType.chara]: CharaInfo;
    [BasicType.couple]: CoupleInfo;
    [BasicType.seiyuu]: SeiyuuInfo;
}
```

## 为什么要面向对象？

因为编程是对现实生活的抽象，有抽象就有具体，这个对象就是我们用来描述具体事物的概念

当我们想用程序描述一个人，这个人有姓名、性别、年龄、身高等属性，也有吃喝拉撒这些功能。

要想面向对象，操作对象，首先便要拥有对象；

要创建对象，必须要先定义类，所谓的类可以理解为对象的模型；

程序中可以根据类创建指定类型的对象；

举例来说：可以通过 Person 类来创建人的对象，通过 Dog 类创建狗的对象，不同的类可以用来创建不同的对象；

JAVA 中一切都需要通过类来操作，java 没有私域函数，所有的函数都只能是对象的方法

## 属性的封装

私有属性，通过添加方法，让私有属性可以被外部访问、修改

### Typescript 到底是在写什么

https://juejin.cn/post/6968636129239105549

现在要用 typescript 来开发，但是我完全不知道在写什么东西

说白了就是要做类型，还有单测要考虑

变量类型的错误到底是怎么检测出来的？ 比如我 希望 a 是一个 number 类型，然后我会用 a 去做运算，但是我也没有真的做运算啊，我只是写了这个函数，这个函数的调用前提是开发者去操作，那这个类型是怎么检测出来的？

答：每次打包的时候，ts 都会进行编译成 js，在这个过程中进行类型检测。就好像我们运行 tsc 命令一样，执行完命令之后，没有报错就是编译通过、类型符合

我编译之后，类型的限制还会有吗？没有了

编译的时候怎么检查一些事件之类的？ 那些从来没有调用过的函数如何检查？

函数不一定要执行才可以检查，就算不进行编译，函数仅仅是有依赖调用，可能最终并没有真实的调优，也可以检查出来，参数、返回值啥的，有点类似于编辑器的智能代码提示功能

### 如何在 Ts 编写阶段就保证运行时顺利？

以前的 typescript 可以靠编译，编译的时候检查

### 必须有返回值，怎么处理？

```ts
    setPlaceholderStyle() {
        // 1. 考虑行内样式的空格
        // 2. 结尾的分号不一定有
        // 3. replace 全局，开发者可能写多个 color
        const placeholderStyle: string = this.data.get('__placeholderStyle');
        const regex = /\s*color\s*:\s*([^;]*)\s*;?\s*/gi;
        let color: string = '';
        // 这里必须有返回值，而且必须是 string，因为replace方法早就定义好了，但是我根本不想返回啊
        placeholderStyle.replace(regex, (match, p1: string): string => {
            if (p1) {
                color = p1;
            }
        });

        if (color) {
            // talos color 关键词仅支持小写
            this.data.set('__placeholderTextColor', color.toLowerCase());
        }
    },
```

### 哪些语法可以用在类型上下文

```ts
type Concat<T extends unkown[], U extends unkown[]> = [...T, ...U];
```

T 和 U 都是数组类型，使用扩展运算符

## 逻辑复用 Vs 类型安全

经常遇到一个问题，不同类型的数据，他们的处理逻辑是相似的，但是他们的类型却细节却有不少的差别，此时想要复用逻辑难免会出现很多联合类型的判断，但是联合类型是更严格的类型，会反过来限制到逻辑的使用，很麻烦

比如 processModuleRelativeRecord

比如 fetchWeeklyInfo

逻辑复用的手段：抽离公共函数，只有这一种

如何做到在逻辑复用里安全？ **消灭联合类型**，保证各处逻辑都是单一类型：

1. 互斥类型
2. 函数重载
3. 类型谓语
4. 泛型约束，体操
5. 分别实现
6. 可识别联合 Discriminated Union
7. 严进严出，中间宽松
    1. 在复用的逻辑内使用公共的
    2. 既然他们的逻辑可以复用，那么他们的类型也一定有重合的部分，所以一定可以抽取一个基础类型
    3. 然后再加上类型注释就好了

数据类型实现 type 上下文的关联

```ts
type Square = {
  kind: 'square';
  size: number;
};

type Rectangle = {
  kind: 'rectangle';
  width: number;
  height: number;
};

type Circle = {
  kind: 'circle';
  radius: number;
}

type Shape = Square | Rectangle | Circle;

function area(shape: Shape): number {
  switch (shape.kind) {
    case 'square':
      return shape.size * shape.size;
    case 'rectangle':
      return shape.width * shape.height;
    case 'circle':
      return Math.PI * shape.radius * shape.radius;
  }
}
```

### 实例一：getMemberInfoOfType

```ts
    getMemberInfoOfType(
        basicType: BasicType,
        projectName: ProjectName,
    ): Promise<MemberInfoOfType<BasicType>> {
        switch (basicType) {
            case BasicType.character:
                return this.charaRepository.find({
                    where: { projectName },
                });
            case BasicType.seiyuu:
                return this.seiyuuRepository.find({
                    where: { projectName },
                });
            case BasicType.couple:
                return this.coupleRepository.find({
                    where: { projectName },
                });
            default:
                return null;
        }
    }
```

返回值是三种不同的类型，charaInfo，sieyuuInfo，coupleInfo。为什么没有办法赋值给 `MemberInfoOfType<Type>`，这种类型函数的结果是什么类型？类型函数的结果取决于泛型的类型，如果没有传入确切的泛型，具体的类型是无法赋值给类型函数的。除非把具体的类型也变成类型函数的结果，然后两者保持一致

```ts
type MemberInfoOfType<Type extends BasicType> = Type extends BasicType.character
    ? CharaInfo[]
    : Type extends BasicType.couple
        ? CoupleInfo[]
        : Type extends BasicType.seiyuu
            ? SeiyuuInfo[]
            : never
```

最终方案：函数重载

```ts
    getMemberInfoOfType(basicType: BasicType.character, projectName: ProjectName): Promise<CharaInfo[]>
    getMemberInfoOfType(basicType: BasicType.seiyuu, projectName: ProjectName): Promise<SeiyuuInfo[]>
    getMemberInfoOfType(basicType: BasicType.couple, projectName: ProjectName,): Promise<CoupleInfo[]>
    getMemberInfoOfType(
        basicType: BasicType,
        projectName: ProjectName,
    ): Promise<CharaInfo[] | SeiyuuInfo[] | CoupleInfo[]>
    getMemberInfoOfType(
        basicType: BasicType,
        projectName: ProjectName,
    ) {
        switch (basicType) {
            case BasicType.character:
                return this.charaRepository.find({
                    where: { projectName },
                });
            case BasicType.seiyuu:
                return this.seiyuuRepository.find({
                    where: { projectName },
                });
            case BasicType.couple:
                return this.coupleRepository.find({
                    where: { projectName },
                });
            default:
                return null;
        }
    }
```

优化方案：函数重载配合类型函数

```ts
    getMemberInfoOfType<Type extends keyof MemberInfoTypeMap>(
        basicType: Type,
        projectName: ProjectName,
    ): Promise<MemberInfoTypeMap[Type][]>
    getMemberInfoOfType(
        basicType: BasicType,
        projectName: ProjectName,
    ) {
        switch (basicType) {
            case BasicType.character:
                return this.charaRepository.find({
                    where: { projectName },
                });
            case BasicType.seiyuu:
                return this.seiyuuRepository.find({
                    where: { projectName },
                });
            case BasicType.couple:
                return this.coupleRepository.find({
                    where: { projectName },
                });
            default:
                return null;
        }
    }
```

感觉是有 bug 的：看类的函数签名，已经是重载后的了

# 实用技巧

## 巧用联合类型

`Dinner` 要么有 `fish` 要么有 `bear` 。

```ts
//   Not good.
interface Dinner1 {
  fish?: number,
  bear?: number,
}

//   Awesome!
type Dinner2 = {
  fish: number,
} | {
  bear: number,
}
```

一些区别：

```ts
let d1: Dinner1 = {}  // Opps
d1 = {fish:1, bear:1} // Opps

let d2: Dinner2 = {}  // Protected!
d2 = {fish:1, bear:1} // Protected!

if ('fish' in d2) {
  // `d2` has `fish` and no `bear` here.
} else {
  // `d2` has `bear` and no `fish` here.
}
```

## 巧用查找类型 + 泛型 +keyof

```ts
interface API {
  '/user': { name: string },
  '/menu': { foods: Food[] },
}
const get = <URL extends keyof API>(url: URL): Promise<API[URL]> => {
  return fetch(url).then(res => res.json())
}
```

上面的定义极大地增强了代码提示：

1. 枚举类型不会有提示
2. 是否必须是字符串？ 属性名形式不带引号可以吗？

## 巧用显式泛型

`$('button')` 是个 DOM 元素选择器，可是返回值的类型是运行时才能确定的，除了返回 `any` ，还可以

```ts
function $<T extends HTMLElement>(id: string):T {
  return document.getElementById(id)
}

// Tell me what element it is.
$<HTMLInputElement>('input').value
```

函数泛型不一定非得自动推导出类型，有时候显式指定类型就好。

### 巧用 as Const

```ts
const fetchOption = {
  mode: 'same-origin',
  credentials: 'include',
};

fetch('/api', fetchOption); // Error!
```

这因为 `mode` 的类型被推导为 `string` 而不是 `'same-origin'` ，`credentials` 同理。

推荐的做法是声明合理的类型：

```ts
const fetchOptions: RequestInit = {
  mode: 'same-origin',
  credentials: 'include',
};
```

如果要的类型很难取到，可以

```ts
const fetchOptions = {
  mode: 'same-origin' as const,
  credentials: 'include' as const,
};

// Or
const fetchOptions = {
  mode: 'same-origin',
  credentials: 'include',
} as const;
```

详见 [const assertions](https://link.zhihu.com/?target=https%3A//www.typescriptlang.org/docs/handbook/release-notes/typescript-3-4.html%23const-assertions) 。

### 巧用 [number] 下标

通常，我们会定义一些枚举：

```ts
type Drink = 'Beer' | 'Wine' | 'Water';
```

以及一个全量的数组，和一个阻止酒吧爆炸的方法：

```ts
const DRINK_LIST: Drink[] = ['Beer', 'Wine', 'Water'];

const checkDrink = (drink: any): drink is Drink => {
  return DRINK_LIST.includes(drink);
};
// 但这并不能保证 `DRINK_LIST` 是枚举值的全量列表：
```

```ts
const DRINK_LIST: Drink[] = ['Beer', 'Wine']; // Oh, I forgot water!
```

或许有某种技巧能定义出期望的全量列表元组。

但这里提供一种简便的写法 —— 先定义全量列表，再获取枚举类型：

```ts
const DRINK_LIST = ['Beer', 'Wine', 'Water'] as const;
type Drink = (typeof DRINK_LIST)[number]; // Equals to 'Beer' | 'Wine' | 'Water'.
```

详见 [indexed-access-types](https://link.zhihu.com/?target=https%3A//www.typescriptlang.org/docs/handbook/2/indexed-access-types.html) 。

关于枚举值，我们也常用到映射，可以配合 [上一篇](https://zhuanlan.zhihu.com/p/64423022) 的【巧用 Record 类型】使用。

### 巧用 Omit + &

有时候，我们希望“继承”一个类型，并且“重写”其中一些属性：

```ts
type Base = {
  foo: number;
  bar: number;
};

// ❌ Interface 'A' incorrectly extends interface 'Base'.
interface A extends Base {
  foo: string;
};

// ❌ B['foo'] is never.
type B = Base & {
  foo: string;
};
可以先 Omit 掉：
```

```ts
interface C extends Omit<Base, 'foo'> {
  foo: string;
};

type D = Omit<Base, 'foo'> & {
  foo: string;
};
```

详见 [Omit](https://link.zhihu.com/?target=https%3A//www.typescriptlang.org/docs/handbook/utility-types.html%23omittype-keys)。

## React

### 巧用类型查找 + 类方法

我们通常会在 React 组件中把方法传下去

```ts
class Parent extends React.PureComponent {
  private updateHeader = (title: string, subTitle: string) => {
    // Do it.
  };
  render() {
    return <Child updateHeader={this.updateHeader}/>;
  }
}

interface ChildProps {
  updateHeader: (title: string, subTitle: string) => void;
}
class Child extends React.PureComponent<ChildProps> {
  private onClick = () => {
    this.props.updateHeader('Hello', 'Typescript');
  };
  render() {
    return <button onClick={this.onClick}>Go</button>;
  }
}
```

其实可以在 `ChildProps` 中直接引用类的方法

```ts
interface ChildProps {
  updateHeader: Parent['updateHeader'];
}
```

两个好处：不用重复写方法签名，能从方法调用跳到方法定义 。

### 巧用 [a, b] as Const

`React.useState()` 返回 `[state, setState]` 的结构，方便调用方解构和命名：

```ts
const [title, setTitle] = React.useState();
```

这是一种很棒的设计，我们也效仿的话：

```ts
const makeGetSet = (initialValue: string) => {
  let value = initialValue;
  const setValue = (v: string) => value = v;
  const getValue = () => value;
  return [getValue, setValue];
};

const [getName, setName] = makeGetSet('14');
const currentName = getName(); // Error! But why?
```

原因是 `[0, '']` 会被推导为类型 `(number | string)[]` 。加 `as const` 可推断为元组：

```ts
const toGetSet = (initialValue: string) => {
  let value = initialValue;
  const setValue = (v: string) => value = v;
  const getValue = () => value;
  return [getValue, setValue] as const;
};

const [getName, setName] = toGetSet('14');
const currentName = getName(); // Great!
```

业务中，自定义 hook 比较多用：

```ts
const useFlag = (initialValue = false) => {
  const [flag, setFlag] = React.useState(initialValue);
  const up = React.useCallback(() => setFlag(true), []);
  const down = React.useCallback(() => setFlag(false), []);
  return [flag, up, down] as const;
};

const [modalVisible, showModal, hideModal] = useFlag();
```

## 增强语法

除了实现 ECMAScript 标准之外，TypeScript 团队也推进了诸多语法提案，比如可选链操作符（`?.`）[11](https://ts.xcatliu.com/introduction/what-is-typescript.html#link-11)、空值合并操作符（`??`）[12](https://ts.xcatliu.com/introduction/what-is-typescript.html#link-12)、Throw 表达式 [13](https://ts.xcatliu.com/introduction/what-is-typescript.html#link-13)、正则匹配索引 [14](https://ts.xcatliu.com/introduction/what-is-typescript.html#link-14) 等。

这些语法，不光是在 ts 中可以使用，甚至有一些在 js 中也可以使用

## ! 非空断言

```
this.element = document.getElementById('foof')!;
```

加一个感叹号，表示不可能为空，让代码提示忽略去

## 初始化一个空对象

可以类型注释初始化一个空数组，但是却不能初始化一个空对象

这是因为 ListFormatWithProject 限制的是元素，formatList 本身只需要是一个数组就可以了

```ts
const formatList: ListFormatWithProject[] = [];
type test = ListFormatWithProject[];
const arr:test = []; 此时则会报错
const projectMap: ProjectMap = {} as ProjectMap;
```

## 类型依赖

class 的一个属性依赖另一个属性的类型：通过泛型和接口属性访问实现

问题在于如何实现多层的

# 体操练习

ts-challenge

ts-toolbelt

```ts
/* eslint-disable max-classes-per-file */
type ProjectName = string;

type ListType = string | number;
class MemberList<TProjectName extends ProjectName, TListType extends ListType, TList extends ListType[]> {
    projectName: ProjectName;

    listType: TListType;

    list: TListType[];

    constructor(projectName: ProjectName, listType: ListType, list: ListType[]) {
        this.projectName = projectName;
        this.listType = listType;
        this.list = list;
    }
}

const seiyuuMemberList = new MemberList('test', 'seiyuu', ['123', '456']);

const { listType } = seiyuuMemberList;
const { list } = seiyuuMemberList;

class CharacterMemberList<T extends ProjectName, U extends ListType, M extends ListType[]> extends MemberList<T, U, M> {
    test: string;
}

const characterList = new CharacterMemberList('test', 'seiyuu', ['123', '456']);

type Simplify<T> = Pick<T, keyof T>;

type bar<T extends ProjectName, U extends ListType, M extends ListType[]> = Simplify<MemberList<T, U, M>>;

interface SomeErrorData {
    someType1: {
        errMsg: string;
    }
    someType2: {
        errMsg: number;
    }
}
class SomeError<T extends keyof SomeErrorData> {
    data: SomeErrorData[T];

    type: T;

    constructor(type: T, data: SomeErrorData[T]) {
        this.data = data;
        this.type = type;
    }
}

const err = new SomeError('someType1', { errMsg: '1' });
```

这么看来 keyof 一个 interface 这种情况下比 enum 好用多了

```ts
interface MemberListType {
    character: {
        name: string;
        romaName: string;
        pixivTag: string;
    }[],
    seiyuu: {
        name: string;
        romaName: string;
        twitterAccount: string;
    }[]
}

class MemberList<ListType extends keyof MemberListType> {
    projectName: string;

    listType: ListType;

    list: MemberListType[ListType]

    constructor(projectName: string, listType: ListType, list: MemberListType[ListType]) {
        this.projectName = projectName;
        this.listType = listType;
        this.list = list;
    }
}

const memberList = new MemberList('muse', 'character', [{
    name: 'honoka',
    romaName: 'honoka',
    pixivTag: '123',
}]);
```

疑难杂症：<https://zhuanlan.zhihu.com/p/82459341>

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

const arr = ['character', 'seiyuu', 'couple'] as const;

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

const characterInfo = foo('character');
const seiyuuInfo = foo('seiyuu');
```

```ts

type Without<T, U> = { [P in Exclude<keyof T, keyof U>]?: never }
type XOR<T, U> = (T | U) extends object
? (Without<T, U> & U) | (Without<U, T> & T)
: T | U

const arr: XOR<string[], number[]> = [];

function test() {
    if (Math.random() > 0.5) {
        return 0;
    }

    return '1';
}

arr.push(test());
```

联合类型不解决这个问题就是无解的，目前看来最好的方法还是创建一个公共类，然后每个 recordService 自己去处理？这样 type 就可以确定了，但是这样和直接调用也没有区别了

或者接受多个泛型，全部类型都自己传，不用推断了

函数重载试一下

type 定死 pixivTag twitterFollower 不要用 default 了

因为 seiyuu 不存在 couple，所以 couple 默认是 characterCouple，把所有的 characterCouple 都去除，只保留 couple

chara

# 装饰器问题@link Es-next-2

TS 的装饰器只能是 JS 的装饰器，所以没必要在 TS 的文档有相关记录
