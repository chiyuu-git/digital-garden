---
{"dg-publish":true,"permalink":"/programming/faq/ui-component/form/"}
---


简单概括一下就是给每个表单域都绑定一个校验函数, 触发了 change 就自动校验

或者是触发了 change 的时候将数据待到上层的 form 组件处, 在 form 组件搜集所有的表单域信息, 然后统一进行校验

如果是多个 form 嵌套, 可以由内层的 form 对外提供一个 validate 函数, 由外层的 form 调用, 内部就是包装了子 form 的所有校验逻辑, 做到在 submit 的时候校验所有的 form

# 表单校验

**表单校验待补全放开, 反正最基本的要求就是增加校验, 大不了我自己给每个字段都加上**

我只加了个 Form.create，没改校验逻辑。你加上校验后去掉这段代码哈

现在 form 整个是不校验的, 需要校验一下

+ 一些接入的组件校验还没有写
+ 开户的校验也没有写
+ 所属银行的校验还没有写

## 表单数据存储于上层组件

通过使用 onFieldsChange 与 mapPropsToFields，可以把表单的数据存储到上层组件或者 san-store 中 注意：mapPropsToFields 里面返回的表单域数据必须使用 Form.createFormField 包装。

## 自行处理表单数据

使用 `Form.create` 处理后的表单具有自动收集数据并校验的功能，但如果您不需要这个功能，或者默认的行为无法满足业务需求，可以选择不使用 `Form.create` 并自行处理数据。

## 自定义校验

我们提供了 `validateStatus` `help` `hasFeedback` 等属性，你可以不需要使用 Form.create 和 decorator，自己定义校验的时机和内容。

1. `validateStatus`: 校验状态，可选 'success', 'warning', 'error', 'validating'。
2. `hasFeedback`：用于给输入框添加反馈图标。
3. `help`：设置校验文案。

## 目前的校验逻辑

通过 `form.create` and `decoration` to validate form item, and then dispatch validateInfo update action. After updating validateInfo store, form item access validateStatus to show feedback

## 提交审核的时候触发 Phone Number 的校验

phone-number 组件由自己的 std-form, 外面的 std-form 貌似不是很好拿到 errInfo

## 长期有效

需要校验两个 radio 是否选择了其中一个, 需要校验是否填了两个 date-picker

## 子 Form 的校验

需要主动触发, draft 校验了就可以了, edit 状态一定是有值的, 就算改成没值也可以通过 change 事件触发子 form 的校验

## Form 嵌套

现在表单校验遇到一个问题, 就是 form 组件嵌套的时候, 外层的 form 无法获取到内部子 form 的表单域信息

导致校验的时候需要额外对封装好的表单组件做处理, 比如定义一个由外部主动调用校验逻辑的方法 ( 需要统一表单 ui-component 的校验错误信息格式, 方便外部做判断是否通过校验).

针对这种情况有什么比较好的解决方法么

搜到一个 antd 的 issue, 不知 san 能不能这样解决, https://github.com/ant-design/ant-design/issues/2677

## promise.all

在组件内封装一下统一格式, 返回 boolean 即可

校验完统一 promise.all

## 逐项校验确认

有很多表单域已经填写了内容但是还是校验不通过

## 自定义组件 Decorator

原理很简单, 给组件的某个位置加上 decorator, 只要能出现 * 就代表加上了 decorator, 然后在 change 事件执行 dispatch 派发就行

## 剩下一个开户行没有数据 一个 Combo 组件

开户行要靠数据连东

combo 组件明天问 haungjing

看看志成是怎么校验的

# Swan Form 组件

使用了 san 的 dispatch（派发）

```js
this.dispatch('form:register', {
    target: this,
    name
});
```

那问题就来了，是在哪个上层组件注册的呢？什么时候注册的呢？又会做哪些处理呢？

> 可以猜想一下，原本 mvvm 搜集表单数据是非常困难的，必须每个表单项目都绑定一个 data

有一个 form 组件，很正常，在 form 组件上维护着各个表单元素的信息，taget 即是表单组件，name 即是表单标签属性 name

```js
    messages: {
        'form:register'({value: {target, name}}) {
            this.formChilds.push({target, name});
        },

        'form:unregister'({value: {target, name}}) {
            this.formChilds = this.formChilds.filter(item => item.name !== name);
        },

        'form:reset'(...args) {
            this.resetHandler(...args);
        },

        'form:submit'(args) {
            this.submitHandler(args);
        }
    },
```

什么时候 submit？由谁来派发？

为什么整个库都没有第二个 form:submit ? form:reset，按道理来说，应该是一个 type submit type reset 的 button 会 dispatch 才对

plugin-button/index.js

```js
this.dispatch(`form:${formType}`);
```

form submit 给谁？开发者自己的服务器？无论是自己搭建的，还是百度提供的，肯定都是 submit 给服务器

```js
this.dispatchEvent('bindsubmit', {
	detail: {
        target: #bindSubmitButton,
        value: formParams
    }
});
```

在哪里监听了 bindsubmit? 是开发者在模板上声明的 bindsubmit

调用了 eventHappen，eventHappen 调用了 communicator，与 master 通信

## 功能确认

准备好 webview 的测试 demo，确认 webview form 的各个功能是实现的，看一下再 naview 环境下是否有限制即可

cts-app 全都是各种订阅校验

1. bind submit
2. bind reset
3. report-ype 为 subscribe
4. report-type 为 default
5. register 和 unregister：useform 就是内部的表单组件注册绑定关系？放到 formChilds 里

以上均没有问题

验证一下 cts-app 的 case
