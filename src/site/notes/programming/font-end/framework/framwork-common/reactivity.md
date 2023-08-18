---
{"dg-publish":true,"permalink":"/programming/font-end/framework/framwork-common/reactivity/"}
---


[SolidJS源码解读 - 用函数闭包实现高性能发布订阅模式 - 掘金](https://juejin.cn/post/7154295953049501727)

令人唏嘘，细粒度的 DOM 更新在十年前被认为是古老而缓慢的技术，可如今 SolidJS 竟然靠它夺得性能冠军，很大一部分功劳都来自于它成功解决了内存消耗问题。

虽然 SolidJS 的语法与 React 几乎一致，但它响应式的实现是和 Vue 一样的发布订阅模式，接下来我们就来看看它是如何实现的。

![](/img/user/programming/font-end/framework/framwork-common/reactivity/image-20230817152244809.png)

# 发布者 `Dep` 的实现

## createSignal

```ts
export function createSignal<T>(value?: T, options?: SignalOptions<T>): Signal<T | undefined> {
  options = options ? Object.assign({}, signalOptions, options) : signalOptions;

  const s: SignalState<T> = {
    value,
    observers: null,
    observerSlots: null,
    comparator: options.equals || undefined
  };

  const setter: Setter<T | undefined> = (value?: unknown) => {
    if (typeof value === "function") {
      if (Transition && Transition.running && Transition.sources.has(s)) value = value(s.tValue);
      else value = value(s.value);
    }
    return writeSignal(s, value);
  };

  return [readSignal.bind(s), setter];
}

```

SolidJS 不像 Vue 那样使用 `Object.defineProperty` 或 `Proxy` 数据劫持实现依赖收集，而是用函数闭包的形式保存依赖，函数内返回 `getter` 和 `setter` 方法，非常的巧妙，不仅减少了这些 API 的大量内存消耗，也解决了 `Proxy` 的目标必须是对象的问题 (没错，吐槽的就是 Vue3 的 `ref.value`)。尤其在响应式数据是大对象或大数组时，由于 `Object.defineProperty` 对所有属性的递归监听，会造成严重的性能问题。

SolidJS 则将对象整体作为一个 `Signal`，更新 `Signal` 需要像 React 一样调用 `setXXX` 方法整体更新，虽然避免了性能问题，但并不完美。

在以下示例中，我们在一个 `Signal` 中存放待办事项列表。为了将待办事项标记为完成，我们需要用克隆对象替换旧的待办事项。尽管在 JSX 中使用 `For` 语句会进行差异对比，但仍然造成了不必要的性能浪费

```ts
const [todos, setTodos] = createSignal([])
setTodos(pre => pre.map((todo) => (todo.id !== id ? todo : { ...todo, completed: !todo.completed }))
```

但这并不代表 SolidJS 不能像 Svelte 一样细粒度的更新对象内的属性，否则怎么对得起细粒度响应式框架的称号，在 SolidJS 中，我们可以使用嵌套的 `Signal` 初始化数据

```ts
const initTodos = () => {
    const res = []
    for (let i = 0; i < 10; i++) {
        const [completed, setCompleted] = createSignal(false);
        res.push({id: i, completed, setCompleted})
    }
    setTodos(res)
}
    
const toggleTodo = (index) => {
    todos()[index].setCompleted(!todo.completed())
}
```

之后我们可以通过调用 `setCompleted` 来更新，而无需任何额外的差异对比。因为我们确切地知道数据要如何变化， 所以我们可以将复杂性转移到数据而不是视图。

```ts
const toggleTodo = (index) => {
    todos()[index].setCompleted(!todo.completed())
}
```

这正是 SolidJS 的魅力所在，基于函数实现的响应式 API 非常自由，既可以避免 `Object.defineProperty` 的全量监听带来的性能浪费，同时支持自定义的细粒度响应式数据控制，将性能压榨到了极致。

## readSignal

```ts
export function readSignal(this: SignalState<any> | Memo<any>) {
  const runningTransition = Transition && Transition.running;
  if (
    (this as Memo<any>).sources &&
    ((!runningTransition && (this as Memo<any>).state) ||
      (runningTransition && (this as Memo<any>).tState))
  ) {
    if (
      (!runningTransition && (this as Memo<any>).state === STALE) ||
      (runningTransition && (this as Memo<any>).tState === STALE)
    )
      updateComputation(this as Memo<any>);
    else {
      const updates = Updates;
      Updates = null;
      runUpdates(() => lookUpstream(this as Memo<any>), false);
      Updates = updates;
    }
  }
  if (Listener) {
    const sSlot = this.observers ? this.observers.length : 0;
    if (!Listener.sources) {
      Listener.sources = [this];
      Listener.sourceSlots = [sSlot];
    } else {
      Listener.sources.push(this);
      Listener.sourceSlots!.push(sSlot);
    }
    if (!this.observers) {
      this.observers = [Listener];
      this.observerSlots = [Listener.sources.length - 1];
    } else {
      this.observers.push(Listener);
      this.observerSlots!.push(Listener.sources.length - 1);
    }
  }
  if (runningTransition && Transition!.sources.has(this)) return this.tValue;
  return this.value;
}

```

`Transition` 的逻辑非主分支逻辑可跳过不看 (此后的这部分逻辑都可跳过)，重点看 `Listener` 分支内的逻辑即可。

此处的 `this` 是 `createSignal` 中创建的响应式数据 `s` ，可以看出，`this.observers` 就是 `Dep`，正如节点级更新框架之名，一个数据对应一个 `Dep`。`Dep` 中存放复数的观察者 `Wathcer`(也就是此处的 `Listener`)，那么 `Listener` 从何而来，那么就要从看接下来的观察者 `Wathcer` 的实现了

## 观察者 `Wathcer` 的实现

![](/img/user/programming/font-end/framework/framwork-common/reactivity/image-20230817152648479.png)

创建 `Wathcer` 是在 `effect` 函数内实现的，`effect` 函数是 `createRenderEffect` 函数的别名

### createRenderEffect

```ts
export function createRenderEffect<Next, Init>(
  fn: EffectFunction<Init | Next, Next>,
  value?: Init,
  options?: EffectOptions
): void {
  const c = createComputation(fn, value!, false, STALE, "_SOLID_DEV_" ? options : undefined);
  if (Scheduler && Transition && Transition.running) Updates!.push(c);
  else updateComputation(c);
}
```

### createComputation

`createComputation` 看似很长，但剔除无关逻辑后只走了这一行

```ts
if (!Owner.owned) Owner.owned = [c];`
```

根据 `fn` 创建出的 `Computation` 赋值给 `Owner.owned`，让 `c` 和 `Owner` 形成相互引用的关系。随后返回 `c` 进入 `updateComputation(c)` 函数

### updateComputation

`updateComputation` 函数中将 `node` ，也就是 `createComputation` 返回的 `c` 赋给了 `Listener`，我们仿佛看见了胜利的曙光

### runComputation

重点关注这一行

```ts
nextValue = node.fn(value);
```

`node.fn` 则是 `() => _el$.value = text()`，在访问 `text()` 时调用 `readSignal`，至此完成了整个依赖收集的过程。

可以看出 SolidJS 作者的 JavaScript 基本功非常扎实，对 **【闭包】【函数是 JavaScript 中的一等公民】** 两项特性运用得淋漓尽致，优雅的实现了高性能发布订阅模式框架。
