---
{"dg-publish":true,"permalink":"/programming/basic/leetcode/offer-58-ii/","tags":["leetcode/rotate","leetcode/pointer","leetcode/space/in-place"]}
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
