---
{"aliases":[],"tags":["leetcode/string","leetcode/pointer/double","leetcode/space/in-place","leetcode/unsolved","leetcode/array/distinct"],"review-dates":[],"dg-publish":true,"difficulty":"easy","date-created":"2023-07-21-Fri, 4:08:31 pm","date-modified":"2023-07-21-Fri, 4:12:41 pm","permalink":"/programming/basic/leetcode/剑指 Offer 05. 替换空格/","dgPassFrontmatter":true}
---


# [剑指 Offer 05. 替换空格](https://leetcode.cn/problems/ti-huan-kong-ge-lcof/)

# Similar Question

# Solution Tips

# 方案一: 双指针 + 原地处理

[代码随想录](https://programmercarl.com/%E5%89%91%E6%8C%87Offer05.%E6%9B%BF%E6%8D%A2%E7%A9%BA%E6%A0%BC.html#%E5%85%B6%E4%BB%96%E8%AF%AD%E8%A8%80%E7%89%88%E6%9C%AC)

```js
var replaceSpace = function(s) {
    s = s.split("");
    let oldLen = s.length;
    let spaceCount = 0;
    for (let i = 0; i < oldLen; i++) {
        if (s[i] === ' ') spaceCount++;
    }
    s.length += spaceCount * 2;
    for (let i = oldLen - 1, j = s.length - 1; i >= 0; i--, j--) {
        if (s[i] !== ' ') s[j] = s[i];
        else {
            s[j - 2] = '%';
            s[j - 1] = '2';
            s[j] = '0';
            j -= 2;
        }
    }
    return s.join('');
};
```
