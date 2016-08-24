---
title: ES6学习笔记-数组的空位
date: 2016-08-24 17:42:15
tags: [ES6, javascript]
---

数组的空位指，数组的某一个位置没有任何值。比如，**Array**构造函数返回的数组都是空位。
```js
Array(3) // [, , ,]
```
上面代码中，Array(3)返回一个具有3个空位的数组。

注意，空位不是**undefined**，一个位置的值等于**undefined**，依然是有值的。空位是没有任何值，in运算符可以说明这一点。
```js
0 in [undefined, undefined, undefined] // true
0 in [, , ,] // false
```

ES5对空位的处理，已经很不一致了，大多数情况下会忽略空位。

- forEach(), filter(), every() 和some()都会跳过空位。
- map()会跳过空位，但会保留这个值
- join()和toString()会将空位视为**undefined**，而**undefined**和**null**会被处理成空字符串。

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
ES6则是明确将空位转为**undefined**。

**Array.from**方法会将数组的空位，转为**undefined**，也就是说，这个方法不会忽略空位。
```js
Array.from(['a',,'b'])
// [ "a", undefined, "b" ]
```
扩展运算符（**...**）也会将空位转为**undefined**。
```js
[...['a',,'b']]
// [ "a", undefined, "b" ]
```
**copyWithin()**会连空位一起拷贝。
```js
[...['a',,'b']]
// [ "a", undefined, "b" ]
```
**fill()**会将空位视为正常的数组位置。
```js
new Array(3).fill('a') // ["a","a","a"]
```
**for...of**循环也会遍历空位。
```js
let arr = [, ,];
for (let i of arr) {
  console.log(1);
}
// 1
// 1
```
上面代码中，数组arr有两个空位，**for...of**并没有忽略它们。如果改成map方法遍历，空位是会跳过的。

**entries()**、**keys()**、**values()**、**find()**和**findIndex()**会将空位处理成**undefined**。
```js
// entries()
[...[,'a'].entries()] // [[0,undefined], [1,"a"]]
// keys()
[...[,'a'].keys()] // [0,1]
// values()
[...[,'a'].values()] // [undefined,"a"]
// find()
[,'a'].find(x => true) // undefined
// findIndex()
[,'a'].findIndex(x => true) // 0
```
由于空位的处理规则非常不统一，所以建议避免出现空位。