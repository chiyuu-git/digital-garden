---
{"aliases":[],"tags":["leetcode/rotate","leetcode/pointer","leetcode/space/in-place"],"review-dates":[],"dg-publish":true,"difficulty":"easy","date-created":"2023-07-21-Fri, 4:56:30 pm","date-modified":"2023-07-21-Fri, 4:57:13 pm","permalink":"/programming/basic/leetcode/剑指 Offer 58 - II. 左旋转字符串/","dgPassFrontmatter":true}
---


# [剑指 Offer 58 - II. 左旋转字符串](https://leetcode.cn/problems/zuo-xuan-zhuan-zi-fu-chuan-lcof/)

# Similar Question

# Solution Tips

# 方案一: 旋转 = 反转

```js
var reverseLeftWords = function (s, n) {
    /** Utils */
    function reverseWords(strArr, start, end) {
        let temp;
        while (start < end) {
            temp = strArr[start];
            strArr[start] = strArr[end];
            strArr[end] = temp;
            start++;
            end--;
        }
    }
    /** Main code */
    let strArr = s.split('');
    let length = strArr.length;
    reverseWords(strArr, 0, length - 1);
    reverseWords(strArr, 0, length - n - 1);
    reverseWords(strArr, length - n, length - 1);
    return strArr.join('');
};
```
