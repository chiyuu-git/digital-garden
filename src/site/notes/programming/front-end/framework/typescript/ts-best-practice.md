---
{"aliases":["ts最佳实践"],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2023-09-29-Fri, 11:08:00 pm","date-modified":"2023-09-29-Fri, 7:58:25 pm","permalink":"/programming/front-end/framework/typescript/ts-best-practice/","dgPassFrontmatter":true}
---


[巧用 Typescript - 知乎](https://zhuanlan.zhihu.com/p/39620591)

[TypeScript：我都传了 type 了，能不能给我自动推导出 data 类型啊？ - 知乎](https://zhuanlan.zhihu.com/p/409083338)

确实是很有用的小体操，第一次了解这种用法就是发现了 dom 的 addEventListener 可以根据 name 自动推断 event 的类型，ctrl 点进去学习了，跟答主描述的如出一辙

# 巧用联合类型

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

# 巧用查找类型 + 泛型 +keyof

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

![](/img/user/programming/front-end/framework/typescript/ts-best-practice/image-20230929195314830.png)

1. 枚举类型不会有提示
2. 是否必须是字符串？ 属性名形式不带引号可以吗？

# 巧用显式泛型

`$('button')` 是个 DOM 元素选择器，可是返回值的类型是运行时才能确定的，除了返回 `any` ，还可以

```ts
function $<T extends HTMLElement>(id: string):T {
  return document.getElementById(id)
}

// Tell me what element it is.
$<HTMLInputElement>('input').value
```

函数泛型不一定非得自动推导出类型，有时候显式指定类型就好。

# 巧用 as Const

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

# 巧用 [number] 下标

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

# 巧用 Omit + &

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

# React

## 巧用类型查找 + 类方法

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

## 巧用 [a, b] as Const

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

# 增强语法

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
