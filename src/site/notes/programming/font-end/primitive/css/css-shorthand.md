---
{"dg-publish":true,"permalink":"/programming/font-end/primitive/css/css-shorthand/"}
---


# Flex-flow 属性对齐

输入一个关键字

输入两个关键字：

+ 两个关键字指定了同一个属性时非法

输入三个关键字：非法，全都采用默认值

是否有必要完全对齐浏览器？ 比如 flex-flow 为了对齐标准会执行很多额外的逻辑，如果开发者写 style 的话，这部分的性能在运行时是有损耗的。

那么为了让开发者体验更好，做的额外逻损失了运行时性能，为了不损失性能又得推荐开发者写 css 而少用行内样式，这又降低了开发者体验（style 动态控制的情况是无法避免的）。

这里该如何平衡呢？如果是开发者使用属性的多寡来平衡似乎会进入死胡同：用得多 -> 对齐浏览器（提升体验） -> 损失性能、最好是写 css -> 影响体验。

最理想的情况是，开发者永远需要是正确的，框架不给开发者兜底，通过最佳实践 + stylelint 约束开发者，提升性能

```js
const FLEX_DIRECTION_KEYWORD_MAP = {'row': 1, 'row-reverse': 1, 'column': 1, 'column-reverse': 1};
const FLEX_WRAP_KEYWORD_MAP = {'wrap': 1, 'nowrap': 1, 'wrap-reverse': 1};

function getCSSPropertyFlexDirection(property, val) {
    return FLEX_DIRECTION_KEYWORD_MAP.hasOwnProperty(val) 
        ? {property: property, value: val} : null;
}

function getCSSPropertyFlexWrap(property, val) {
    return FLEX_WRAP_KEYWORD_MAP.hasOwnProperty(val) 
        ? {property: property, value: val} : null;
}
// style_propery_shorthand.cc
function flexFlowShortHandle() {
    return [
        {property: 'flexDirection', parser: getCSSPropertyFlexDirection},
        {property: 'flexWrap', parser: getCSSPropertyFlexWrap}
    ];
}

// 这个是种通用逻辑，针对这类 shorthand property 都可以使用
function consumeShorthandGreedilyViaLonghands(
    shorhand,
    valueTokenList
) {
    const longhands = [];

    for (const valueToken of valueTokenList) {
        let foundLonghand = false;
        for (let i = 0; !foundLonghand && i < shorhand.length; i++) {
            if (longhands[i]) {
                continue;
            }

            longhands[i] = shorthand[i].parser(shorthand[i].property, valueToken);
   
            // 找到一个之后就不会再继续了，因为一个关键字只能匹配一个属性，没啥问题
            if (longhands[i]) {
                foundLonghand = true;
            }
        }

        // valueToken 其中一个错了的话，直接返回 null
        // 同时 写多了，但是没有写错 也会返回 null
        // 写了相同的，导致第二个没有匹配到，也会返回 null？ 相当于是 写多了 没写错的情况
        if (!foundLonghand) {
            return null;
        }
    }

    const res = [];
    for (let i = 0; i < shorthand.length; i++) {
        if (longhands[i]) {
            res.push(longhands[i]);
        }
        // 写少了才会进入这里
        else {
            res.push({property: shorthand[i].property, value: 'initial'});
            // 我们支持 initial 么？
        }
    }
}

// shorthands_custom.cc
rules.flexFlow = (value) => {
    const valueTokenList = value.split(/\s+/);
    const res = consumeShorthandGreedilyViaLonghands(flexFlowShortHandle(), valueTokenList);
    // ...
}
```

# Border 简写属性

```ts
        if (props.hasOwnProperty('borderStyle') && props.borderStyle !== 'none') {
            if (!props.hasOwnProperty('borderWidth')) {
                // borderWidth 默认值为 medium ，等价于 3px
                props.borderWidth = 3;
            }

            if (!props.hasOwnProperty('borderColor') && props.hasOwnProperty('color')) {
                // borderColor 默认值为 currentColor，即是当前元素的 color 属性值
                props.borderColor = props.color;
            }

        }
```

在这里处理没用，必须要在 styleAdapter 处理， 如果为 none 把 borderStyle 等 delete 掉

border 没有 none 这个关键字，h5 下写 none 触发简写属性短路

```ts
function handleBorder (val: string, platform, side: string = ''): KeyValue[] {
    if (val === 'none') {
        // 在这里处理没用，必须要在 styleAdapter 处理， 如果为 none 把 borderStyle 等 delete 掉
        return null;
    }
}
```

## 重置简写属性是一个麻烦的工作

必须要在后置处理，如果简写属性是 none 之类的，需要逐个删除

## 简写属性难题

如果先写了 border 再写了 longhand 那要如何覆盖呢？无法判断 shorthand 和 longhand 谁先谁后，只能是在 shorthand 重置为 none 时就直接做删除，做不了，样式解析时拿不到 props

所以要用同一的简写属性处理，如果触发了短路，返回 longhand 属性的默认值

## 默认值最好是放在端上，前端没有必要传来传去的，反正都是默认值

## 简写属性的贪心算法肯定是慢点的

```js
// 为 number 保留 n 位小数，默认为 4
function roundFun (value, n = 4) {
    return Math.round(value * Math.pow(10, n)) / Math.pow(10, n);
}

function handleNumber (val, fn = parseFloat) {
    if (typeof val !== 'number') {
        let number = fn(val);
        if (isNaN(number)) {
            return null;
        }

        // 保留小数点后 4 位
        // 客户端接受数值精度 4 位即可，减少通信体积
        number = roundFun(number);

        // 支持vw/vh
        if (val.endsWith('vh')) {
            return number + 'vh';
        }
        if (val.endsWith('vw')) {
            return number + 'vw';
        }
        return number;
    }
    return roundFun(val);
}

const BORDER_WIDTH_KEYWORD_MAP = {
    thin: 1,
    medium: 3,
    thick: 5
};

function handleBorderWidth (val) {
    return BORDER_WIDTH_KEYWORD_MAP[val] || handleNumber(val);
}

function handleBorder (val, platform, side) {
    const res = [];
    const valList = val.trim().split(/\s+/);
    for (let i = 0; i < valList.length; i++) {
        switch (i) {
            case 0:
                res.push({ property: `border${side}Width`, value: handleBorderWidth((valList[i])) });
                break;
            case 1:
                res.push({ property: `border${side}Style`, value: valList[i] });
                break;
            case 2:
                res.push({ property: `border${side}Color`, value: valList[i] });
                break;
        }
    }
    return res;
}

handleBorder('3px solid black');
```

```js
// 为 number 保留 n 位小数，默认为 4
function roundFun (value, n = 4) {
    return Math.round(value * Math.pow(10, n)) / Math.pow(10, n);
}

function handleNumber (val, fn = parseFloat) {
    if (typeof val !== 'number') {
        let number = fn(val);
        if (isNaN(number)) {
            return null;
        }

        // 保留小数点后 4 位
        // 客户端接受数值精度 4 位即可，减少通信体积
        number = roundFun(number);

        // 支持vw/vh
        if (val.endsWith('vh')) {
            return number + 'vh';
        }
        if (val.endsWith('vw')) {
            return number + 'vw';
        }
        return number;
    }
    return roundFun(val);
}


/**
 * border 的简写样式
 */
const BORDER_WIDTH_KEYWORD_MAP = { 'thin': 1, 'medium': 3, 'thick': 5 };
const BORDER_STYLE_KEYWORD_MAP = { 'solid': 1, 'dotted': 1, 'dashed': 1 };

function getCSSPropertyBorderWidth(property, val) {
    return { property: property, value: BORDER_WIDTH_KEYWORD_MAP[val] || handleNumber(val) };
}

function getCSSPropertyBorderStyle(property, val) {
    return BORDER_STYLE_KEYWORD_MAP.hasOwnProperty(val)
        ? { property: property, value: val } : null;
}

function getCSSPropertyBorderColor(property, val) {
    return { property: property, value: val };
}

const borderShorthand = [
    { property: 'borderWidth', parser: getCSSPropertyBorderWidth },
    { property: 'borderStyle', parser: getCSSPropertyBorderStyle },
    { property: 'borderColor', parser: getCSSPropertyBorderColor }
];

function consumeShorthandGreedilyViaLonghands(
    shorthand,
    valueTokenList
) {
    const longhands = [];

    for (const valueToken of valueTokenList) {
        let foundLonghand = false;
        for (let i = 0; !foundLonghand && i < shorthand.length; i++) {
            // 已经匹配过的单个属性不再处理
            if (longhands[i]) {
                continue;
            }

            longhands[i] = shorthand[i].parser(shorthand[i].property, valueToken);

            // 当前 token 匹配成功，结束内层循环，去匹配下一个 token
            if (longhands[i]) {
                foundLonghand = true;
            }
        }

        // 任意一个 token 没有匹配成功，简写属性返回 null，比如：
        // 1. valueTokenList 长度超出简写属性定义，超出的部分无法匹配成功
        // 2. 长度小于等于定义，错误的 token 或者 相同属性的 token 也会导致匹配失败
        if (!foundLonghand) {
            return null;
        }
    }

    const res = [];
    for (let i = 0; i < shorthand.length; i++) {
        if (longhands[i]) {
            res.push(longhands[i]);
        }
        // na 下如果要声明属性默认值，最好的选择是不传递属性，减少数据体积，默认值由端上处理即可
        // else {
        // res.push({ property: shorthand[i].property, value: 'initial' });
        // }
    }

    return res.length > 0 ? res : null;
}

function handleBorderShorthand(val) {
    const valueTokenList = val.split(/\s+/);
    return consumeShorthandGreedilyViaLonghands(borderShorthand, valueTokenList);
}

handleBorderShorthand('3px solid black');

```

## 简写属性取消机制

设置了简写属性之后再将简写属性设置为 unset

全部都返回 null
