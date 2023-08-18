---
{"dg-publish":true,"permalink":"/programming/font-end/framework/react/react-hook-advanced/"}
---


# Hooks 与 Fiber 的关系

函数式组件只是一个函数，它本身无法保持状态，但是我们使用 useState 就可以取到之前的状态，给我们的感觉就是，函数式组件不知道在哪里有个仓库，我们使用 useState 就是从仓库中取了数据。

那么这个仓库到底在哪呢？我们知道，js 中函数只是一个特殊的对象，它也可以有各种属性，这个仓库挂在我们的函数组件身上似乎也合情合理，但是函数不是类，挂在函数身上的属性不能靠 new 弄出来一个新的。

所以如果想要挂在函数自身, 就无法满足组件重用的需求.

如果是 v16 之前的版本，react 应该会把 hooks 放在 vdom 上，而 v16 之后，有了 fiber，保存 fiber 的职责也就交给了 fiber。

> 注意：Hooks 的思想是一个通用的思想，并不与 fiber 或者函数组件绑定，有很多框架都实现了 hooks，比如 Vue、比如 preact。或者说是因为函数式组件使用 Hooks 可以很轻松的解决**维持状态**的问题，所以对 Hooks 青睐有加。

# Hooks 在 Fiber 上是什么样子的

```jsx
import { useCallback, useEffect, useMemo, useRef, useState } from 'react';
import './App.css'

function App() {
    const [state1, setState1] = useState("state-1");
    const [state2, setState2] = useState('state-2');

    const counter = {
      value: 0,
      add(){
        this.value++
      }
    }
  
    const handler = useCallback(() => {
      setState1('state-1 改变了');
    }, [1]);

    const counterRef = useRef(counter);
  
    useEffect(() => {
      counterRef.current.add()
      console.log(`useEffect回调被执行了 ${counterRef.current.value} 次`);
    }, [state1]);
  
    useEffect(() => {
      console.log(`counter的值为: ${counter.value}, counterRef.current.value 的值为: ${counterRef.current.value}`);
    }, [counterRef.current.value]);
  
    useMemo(() => {
      return 'memo-content';
    }, [])

  return (
    <div className="App">
      <header className="App-header">
        <p onClick={handler}>
          {state1}
        </p>
      </header>
    </div>
  );
}
```

在组件处打个断点，就可以看到 React 的执行到调用函数组件位置时的调用栈。

![img](/img/user/programming/font-end/framework/react/react-hook-advanced/imageDownloadAddress-20230817120750451.png)

可以看到，上一个执行的函数名叫 renderWithHooks，很容易看出了它就是与 Hooks 相关的函数，我们来到这个函数，就可以看到，里面有个叫 workInProgress 的变量是一个 FiberNode 类型的对象，它就是我们这个函数组件对应的 Fiber 节点。

fiber 节点上有个 memorizedState 属性，他就是 Hooks 存放数据的地方：

![img](/img/user/programming/font-end/framework/react/react-hook-advanced/imageDownloadAddress-20230817120750837.png)

展开所有的链表：会发现，我们在 App 组件中使用的 hooks 被按照顺序链结在了一起。通过链表上节点的 memoizedState 属性可以看出这个节点对应哪种 hooks。

![img](/img/user/programming/font-end/framework/react/react-hook-advanced/imageDownloadAddress-20230817120750668.png)

这就是 hooks 存取数据的地方，执行的时候各自在自己的那个 memorizedState 上存取数据，完成各种逻辑，这就是 hooks 的原理。

# Hooks 分类

- 有的是同时需要取数据和更新函数（useState，useReducer，useTransition）
- 有的只取数据（useMemo，useCallback，useRef，useId，useContext）
- 有的只取更新函数（useDeferredValue）
- 有的是根据条件执行一些逻辑，并没有返回值（useEffect，useImperativeHandle）

**更新函数**就是用来更新数据和视图的，由 React 内部实现

但是他们的本质都是相同的——从挂载在组件对应的 Fiber 节点（其实完全可以理解为 vdom）上的缓存数据中获得旧的值，结合此次新传入的值，返回一个值或者执行某些代码。重点就是下面三个部分，不同的 Hooks 对这三个要素的使用是不同的:

- 从缓存数据中获得旧的值
- 新传入的值
- 执行逻辑

关于对这三个要素使用的不同可以列举一些例子：

- useState：**除了初始化，只需要缓存的值**，从缓存数据中获得旧的值和更新函数，但是对 useReducer 的更新逻辑进行了一定的简化，摇身一变成了最常用的获取状态的 Hooks；
- useReducer：**理论上除了初始化，只需要缓存的值，但是如果非要每次使用不同的** **reducer 就没办法了**，从缓存数据中获得旧的值和更新函数，可以做更多的自定义，甚至结合 Context 实现全局状态管理；
- useTransition：**只需要缓存的值**，从缓存数据中获得 pending 值和更新函数，相当与一个异步状态和一个 Loading 状态的封装；
- useMemo：**需要传入的值和缓存的值**, 对比新旧值是否相同，决定是返回缓存的值，还是用传入的函数重新计算一个值返回并更新缓存内容；
- useCallback：相当于返回值是一个函数的 useMemo；
- useRef：**只需要缓存的值**，也只返回初始化的值；
- useContext：**只需要传入的值；**
- useDeferredValue：**需要传入的值和缓存的值**，React 将会先尝试使用旧值进行重新渲染（因此它将返回旧值），然后再在后台使用新值进行另一个重新渲染（渲染完成后它将返回更新后的值）
- useEffect: **需要传入的值和缓存的值**, 新传入的值有依赖数组和副作用函数，如果依赖数组内的依赖项变化了，就执行副作用函数。
- useImperativeHandle：**需要传入的值和缓存的值**, 新传入的值有依赖数组和返回句柄的函数和 ref ，如果依赖数组内的依赖项变化了，就重新执行返回句柄的函数获得新的句柄返回并更新旧的句柄。

在组件第一次挂载的时候, Hooks 的 memoizedState 也会进行挂载，然后组件更新的时候就可以直接从 memoizedState 取旧数据，每个 useXxx 的 hooks 都有 mountXxx 和 updateXxx 两个阶段，比如 ref 就是 mountRef 和 updateRef

# 常用 Hooks 实现原理

## useRef

它的代码是最简单的，只有这么几行：

```tsx
function mountRef<T>(initialValue: T): {|current: T|} {
    const hook = mountWorkInProgressHook();
    const ref = {current: initialValue};
    hook.memoizedState = ref;
    return ref;
}

function updateRef<T>(initialValue: T): {|current: T|} {
    const hook = updateWorkInProgressHook();
    return hook.memoizedState;
}
```

mountWorkInProgressHook 是创建并返回 memorizedState 链表的

updateWorkInProgressHook 是更新并返回 memorizedState 链表的

```tsx
function mountWorkInProgressHook(): Hook {
    const hook: Hook = {
        memoizedState: null,
        baseState: null,
        baseQueue: null,
        queue: null,
        next: null,
    };

    if (workInProgressHook === null) {
        // This is the first hook in the list
        currentlyRenderingFiber.memoizedState = workInProgressHook = hook;
    } else {
        // Append to the end of the list
        workInProgressHook = workInProgressHook.next = hook;
    }
    return workInProgressHook;
}
```

这些不用管，只要知道修改的是对应的 memorizedState 链表中的元素就行了。

可以看到是把传进来的 value 包装了一个有 current 属性的对象，冻结了一下，然后放在 memorizedState 属性上。

后面 update 的时候，没有做任何处理，直接返回这个对象。

所以，useRef 的功能就很容易猜到了：**useRef 可以保存一个数据的引用，这个引用不可变**

## useMemo

![](/img/user/programming/font-end/framework/react/react-hook-advanced/image-20230817125059369.png)

useMemo 在 memorizedState 上放了一个数组，第一个元素是传入函数的执行结果，第二个是传入的 deps

更新的时候也是取出之前的 memorizedState，和新传入的 deps 做下对比，如果没变，就返回之前的值，也就是 `prevState[0]`

如果变了，创建一个新的数组放在 memorizedState，第一个元素是新传入函数的执行结果，第二个元素是 deps。

所以，useMemo 的功能大家也能猜出来：**useMemo 可以实现函数执行结果的缓存，如果 deps 没变，就直接拿之前的，否则才会执行函数拿到最新结果返回。**

## useState

state 改了之后是要触发更新的调度的，React 有自己的调度逻辑，就是我们前面提到的 fiber 的 schedule，所以需要 dispatch 一个 action。

```tsx
function mountState<S>(initialState: (() => S) | S): [S, Dispatch<BasicStateAction<S>>] {
    const hook = mountWorkInProgressHook();
    if (typeof initialState === 'function') {
        initialState = initialState();
    }
    hook.memoizedState = hook.baseState = initialState;
    // 这个是更新任务
    const queue: UpdateQueue<S, BasicStateAction<S>> = {
        pending: null,
        lanes: NoLanes,
        dispatch: null,
        lastRenderedReducer: basicStateReducer,
        lastRenderedState: (initialState: any),
    };
    hook.queue = queue;
    const dispatch: Dispatch<BasicStateAction<S>> =
        (queue.dispatch = (dispatchSetState.bind(null, currentlyRenderingFiber, queue): any));
    return [hook.memoizedState, dispatch];
}

function updateState<S>(initialState: (() => S) | S): [S, Dispatch<BasicStateAction<S>>] {
    return updateReducer(basicStateReducer, (initialState: any));
}
```

## useEffect

同样的，effect 传入的函数也是被 React 所调度的：

![img](/img/user/programming/font-end/framework/react/react-hook-advanced/imageDownloadAddress-20230817125227533.png)

hooks 负责把这些 effect 串联成一个 updateQueue 的链表，然后让 React 去调度执行。

![img](/img/user/programming/font-end/framework/react/react-hook-advanced/imageDownloadAddress-20230817125227106.png)

# Hooks 限制

## 自定义 Hooks 只能复用逻辑，不能创造 Hooks

实际上，我们只能使用对 React 提供的 Hooks 或者其他自定义 hook 进行一层逻辑的封装，并不能自己创造 Hooks。也就是说，实际上我们可以把那些自定义 Hooks 都拍平，拍平之后还是那几个 React 提供的 Hooks。

这是因为这些 Hooks 是依赖 React 执行流程和内部数据结构的，想要增加 Hooks 要动 React 本身的代码。

## 必须按照相同的顺序调用 Hooks ，不能乱序，不能动态增加或者减少

Hooks 不能放在判断逻辑，异步逻辑里，必须保证：函数组件执行过程中能够同步的执行到所有的 Hooks，否则就会出错。

这是因为 Hooks 实现的方式，我们调用 Hooks 往往是为了拿到某个之前的状态（useState，useContext）或者根据依赖做出一些操作（useEffect，useMemo），这时就需要拿到之前的状态或者依赖，为了能够拿到这些东西，React 使用的策略是：将旧的状态根据调用的先后顺序组成链表，挂载到 fiber 上一个名为 memoizedState 的属性上，更新的时候遇到 Hooks 就可以通过这个链表按顺序取旧的状态。

> 实际实现会复杂一些，比如 useContext 并不会被挂在链表上，而是直接从给 useContext 传入的参数获取，但是保证 Hooks 按顺序调用的逻辑还是会检查到这个错误并抛出异常。
