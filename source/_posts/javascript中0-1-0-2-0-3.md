---
title: javascript中0.1 + 0.2 != 0.3?
date: 2016-11-20 19:32:55
tags: [javascript]
---

0.1+0.2 等于0.3吗？相信拿着这条题目随便问一个高年级的小学生，他们都会毫不犹豫都回答：相等。是的，相等是正常的，这是常识。但是都说实践是检验真理的唯一标准，拿这道简单的算术题用javascript在chrome控制台试验一下：

结果令人大跌眼镜，`在控制台输入0.1+0.2 == 0.3返回的结果竟然是false`我们输入0.1+0.2，看看结果，竟然是`0.30000000000000004`。

这是为什么呢？在《Javascript权威指南》中有提到，`JS是不区分整数和浮点数的`，JS采用的是IEEE 754标准定义的64位浮点格式表示数字，所以JS中的所有数字都是浮点数。按照JS的数字格式，整数有的范围是`-2^53 ~ 2^53`，而且只能表示有限个浮点数，能表示的个数为`2^64 − 2^53 + 3`个。至于为什么是这个范围，可以具体看看《JavaScript 中的数字》这篇文章也解下。而我们都知道，浮点数的个数是无限的，这就导致了JS不能精确表达所有的浮点数，而只能是一个近似值。

那怎样才能比较`0.1+0.2 == 0.3`呢？既然浮点数是一个近似值，那我们可以认定在某个可以接受的精度范围内，他们是相等的。因此可以定义一个比较函数来比较浮点数。
``` js
	function isFloatEqual(f1,f2,digits) {
	    return f1.toFixed(digits) === f2.toFixed(digits);
	} 
	isFloatEqual(0.1+0.2,0.3,10);   //  返回true
```

可以看到，这时再比较0.1+0.2就与0.3相等了。这种比较，其实是字符串的比较，toFixed方法，返回的是一个字符串。可以在控制台中输入：
```js
	0.1.toFixed(1)+0.2.toFixed(1)
```
你会发现，返回的结果是"0.10.2"

这种方法解决了0.1+0.2==0.3的问题，但是当我用isFloatEqual(0.3 - 0.2,0.1,10)的时候，发觉返回的值是false，这又是为什么呢？

原来0.3 - 0.2计算出来的是0.09999999999999998，上面的方法只能覆盖到计算结果比实际结果大的情况，而对于小的情况无能为力。

我们知道，当两个数相减，如果差在约定的精度范围内，我们就可以认为这两个数相等，根据这个原理，再来重写isFloatEqual函数，如下：
```js
	function isFloatEqual(f1,f2,digits) {
	    return Math.abs(f1 - f2) < digits;
	} 
	isFloatEqual(0.1+0.2,0.3,0.001);   // true
	isFloatEqual(0.3-0.2,0.1,0.001); // true
```

那除了上面的方法外，能不能让JS在做加减法的时候精确一点呢？
我们知道，只要在-2^53 ~ 2^53的范围内，JS做整数的加减运算是精确的，那么我们是不是可以将浮点数转换为整数，再进行运算呢？按照这个思路，在《浅谈JavaScript浮点数及其运算》这篇文章中，提供了一种方法，将代码粘贴如下：
```js
	function accAdd(arg1,arg2){
		var r1,r2,m;
		try{r1=arg1.toString().split(".")[1].length}catch(e){r1=0}
		try{r2=arg2.toString().split(".")[1].length}catch(e){r2=0}
		m=Math.pow(10,Math.max(r1,r2))
		return (arg1*m+arg2*m)/m
	}
```

这种方法是先计算出两个浮点数的小数位数n,两个参数再分别与10^n（放大倍数）相乘，以达到对两个参数取整的目的，再用整数来进行相加运算，加完后除掉放大的倍数就可以得出结果了。

现在用accAdd(0.1,0.2)，返回的结果是0.3了，但是评论里面马上有人提出accAdd(2.22,0.1) 的结果不是2.32，而是2.3200000000000003。原来在做arg1*m这一步时，依旧是浮点数运算，所以返回的结果不一定是整数，依旧是浮点数。

我看到作者在做减法的时候用了toFixed()，如果用toFixed(n)是不是就可以了呢？来试一下，函数改造成这样：
```js
	function accAdd(arg1,arg2){
		var r1,r2,n,m;
		try{r1=arg1.toString().split(".")[1].length}catch(e){r1=0}
		try{r2=arg2.toString().split(".")[1].length}catch(e){r2=0}
		n = Math.max(r1,r2);
		m=Math.pow(10,n);
		return ((arg1*m+arg2*m)/m).toFixed(n)
	}
```
这次再调用accAdd(2.22,0.1)时，返回的是2.32了，完美！

但是手贱试了下accAdd(2.22,0.08),返回的结果是2.30，而我期望返回的结果是2.3。上文已经提到过，toFixed返回的是一个字符串，所以用toFixed返回2.30看来是正常的了。那如果要返回2.3要怎样做呢？

其实很简单，用parseInt将arg1*m转换为整数就可以了。
```js
	function accAdd(arg1,arg2){
		var r1,r2,m;
		try{r1=arg1.toString().split(".")[1].length}catch(e){r1=0}
		try{r2=arg2.toString().split(".")[1].length}catch(e){r2=0}
		m=Math.pow(10,Math.max(r1,r2))
		return (parseInt(arg1*m,10)+parseInt(arg2*m,10))/m
	}
```
然并卵，这个计算依然是不准确的，有空再想想。