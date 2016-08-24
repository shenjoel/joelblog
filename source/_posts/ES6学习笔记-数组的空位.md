---
title: ES6学习笔记-数组的空位
date: 2016-08-24 17:42:15
tags: [ES6, javascript]
---

数组的空位指，数组的某一个位置没有任何值。比如，Array构造函数返回的数组都是空位。
```js
Array(3) // [, , ,]
```
上面代码中，Array(3)返回一个具有3个空位的数组。

注意，空位不是undefined，一个位置的值等于undefined，依然是有值的。空位是没有任何值，in运算符可以说明这一点。
```js
0 in [undefined, undefined, undefined] // true
0 in [, , ,] // false
```

ES5对空位的处理，已经很不一致了，大多数情况下会忽略空位。

- forEach(), filter(), every() 和some()都会跳过空位。
- map()会跳过空位，但会保留这个值
- join()和toString()会将空位视为undefined，而undefined和null会被处理成空字符串。

```js
// forEach方法
[,'a'].forEach((x,i) => console.log(i)); // 1
// filter方法
['a',,'b'].filter(x => true) // ['a','b']
// every方法
[,'a'].every(x => x==='a') // true
// some方法
[,'a'].some(x => x !== 'a') // false
// map方法
[,'a'].map(x => 1) // [,1]
// join方法
[,'a',undefined,null].join('#') // "#a##"
// toString方法
[,'a',undefined,null].toString() // ",a,,"
```
ES6则是明确将空位转为undefined。

> ****由于空位的处理规则非常不统一，所以建议避免出现空位。****
