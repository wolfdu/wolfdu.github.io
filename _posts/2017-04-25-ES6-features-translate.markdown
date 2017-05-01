---
layout:     post
title:      "纵观JavaScript ES6 核心特性"
subtitle:   " \"ECMAScript 6 Or ES2015+\""
date:       2017-04-25 13:57:00
author:     "Wolfdu"
header-img: "img/in-post/ES6-features-translate/es6-translate-home.jpg"
catalog:    true
tags:
    - Translate
    - JavaScript
---

> 本文仅为翻译学习

JavaScript 在最近几年发生了相当大的变化。这里有12个你可以马上使用的新特性！！！

# 1.JavaScript 前世今生

新增加的语言被称作ECMAScript 6，同时也可以称为ES6或者ES2015+。

JavaScript自诞生以来，发展进化的比较缓慢。每隔几年才会产生新的内容。ECMAScript在1997年成为JavaScript的语言实现规范。ECMAScript已经发布了几个版本如：ES3，ES5，ES6等等。
![java-javascript](/img/in-post/ES6-features-translate/history-javascript-evolution-es6.png)
如你所见，从版本ES3，ES5和ES6分别间隔了10年和6年。像ES6这种大量修改再发布的旧模式已经被每年都做一些小的修改或更新的模式所代替了。

# 2.浏览器支持

所有的现代浏览器和环境都已经支持ES6!

![java-javascript](/img/in-post/ES6-features-translate/es6-javascript-support.png)
<small class="img-hint">[图源](https://kangax.github.io/compat-table/es6/)</small>

Chrome，MS Edge，Firefox，Safari，Node和很多其他的环境都已经内置支持了ES6的大部分功能。所以，你在本教程中学习到所有知识，你都能够马上应用。

让我们开始学习ECMAScript 6！

# 3.ES6核心特性

你能在你的浏览器控制台中测试所有的代码片段！

![java-javascript](/img/in-post/ES6-features-translate/javascript-es6-classes-on-browser-console.png)

不要太相信我所说的而是要去测试每个ES5和ES6的栗子。让我们开始吧！！！

## 3.1变量的块级作用域

使用ES6，我们从声明变量开始使用`let`/`const`替换`var`。

使用`var`有什么问题呢？

使用`var`声明变量的问题是变量会泄露到其它的代码块中如：`for`循环和`if`代码块。

	// ES5

	var x = 'outer';
	function test(inner) {
	  if (inner) {
	    var x = 'inner'; // 作用域为整个 function
	    return x;
	  }
	  return x; // 因为第四行x声明提升了，所以这里的x被重新定义了
	}
	test(false); // undefined 😱
	test(true); // inner

在运行 `test(false)` 时你可能会期望函数返回 `outer` ，但是并没有，你得到的只有 `undefined` 。

为什么会这样呢？？？

因为即使这里的if块不执行，第四行的表达式 `var x` 会被提升执行。

> var **提升**

> * `var` 是函数作用域。它在整个函数中甚至在声明之前都是可用的。
> * 声明提升。因此你可以在声明之前使用一个变量。
> * 初始化是不被提升的。如果你使用 `var` 请总是将它声明在顶部。
> *在应用了声明规则之后，我们就能很好的明白这里面都发生了些什么：<br/>

	var x = 'outer';
	function test(inner) {
	  var x;//声明提升
	  if (inner) {
	    var x = 'inner'; //初始化不提升
	    return x;
	  }
	  return x; 
	}

ECMAScript 2015 能够解决这个问题：

	// ES6

	let x = 'outer';
	function test(inner) {
	  if (inner) {
	    let x = 'inner';
	    return x;
	  }
	  return x; // 获取第一行预期的结果
	}
	test(false); // outer
	test(true); // inner

使用 `let` 替换 `var` 让代码同预期一样运行。如果if代码块没有被调用那么变量`x`不会在if块之外被提升声明。

> Let **提升**和“暂存死区(*temporal dead zone*)”

> *在ES6中， `let`会提升变量到代码块的顶部（不是像ES5那样，提升到function顶部）。
> *然而，在代码块中在变量声明前引用会导致一个 `ReferenceError`异常。
> *`let`是块级作用域，你不能在声明前使用。
> *“暂存死区(*temporal dead zone*)”是只代码块开始到变量声明的这段区域。

**IIFE**

在解释IIFE之前，我们先看一个栗子。来看一下：
	
	//ES5

	{
	  var private = 1;
	}
	console.log(private); // 1

如你所见，private泄露出去了。你需要去使用IIFE(immediately-invoked function expression,立即执行的函数表达式)去包含它：

	//ES5

	(function(){
	  var private2 = 1;
	})();
	console.log(private2); // 未经处理的异常 ReferenceError

如果你看一看jQuery/lodash 或者其他的开源项目，你会注意到他们使用IIFE来避免污染全局环境，并且他们只在全局环境上定义了如：_,$和jQuery。

在ES6中就简单明了多了，我们不在需要使用IIFE，当我们只需要使用代码块和`let`:
	
	//ES6

	{
	  let private3 = 1;
	}
	console.log(private3); // 未经处理的异常 ReferenceError

**Const**

当你希望一个变量保持不变（常量）的时候，你可以使用`const`来声明它。

![java-javascript](/img/in-post/ES6-features-translate/javascript-es6-const-variables-example.png)

> 总之：使用`let`和`const`忘掉`var`吧。

> * 对所有引用使用`const`，避免使用`var`。
> * 如果你必须重新分配引用，使用`let`替换`const`。

## 3.2 模板字面量

当我们有了模板字面量之后，我们就不用再去做过多的字符串拼接了。
我们来对比一下：

	ES5
	
	var first = 'Du';
	var last = 'Yuehan';

	console.log('My name is '+first+last);

现在你可以使用反引号(`)和字符串插入值 ``${}``:

	ES6

	const first = 'Du';
	const last = 'YueHan';

	console.log(`My name is ${first}${last}`);

## 3.3 多行字符串

我们不再使用+`\n`来拼接字符串了，就像这样：

	ES5

	var template = '<li *ngFor="let todo of todos" [ngClass]="{completed: todo.isDone}" >\n' +
	'  <div class="view">\n' +
	'    <input class="toggle" type="checkbox" [checked]="todo.isDone">\n' +
	'    <label></label>\n' +
	'    <button class="destroy"></button>\n' +
	'  </div>\n' +
	'  <input class="edit" value="">\n' +
	'</li>';
	console.log(template);	

在ES6中我们可以使用反引号来解决这个问题：

	ES6

	const template = `<li *ngFor="let todo of todos" [ngClass]="{completed: todo.isDone}" >
	  <div class="view">
	    <input class="toggle" type="checkbox" [checked]="todo.isDone">
	    <label></label>
	    <button class="destroy"></button>
	  </div>
	  <input class="edit" value="">
	</li>`;
	console.log(template);

这两段代码都就有同样的输出。

## 3.4 解构(Destructuring)赋值

ES6的解构是非常有用且简洁的。举个栗子：

**从数组中获取元素**

	ES5

	var array = [1, 2, 3, 4];
	var first = array[0];
	var third = array[2];
	console.log(first, third); // 1 3

等同于：

	ES6

	const array = [1, 2, 3, 4];
	const [first, ,third] = array;
	console.log(first, third); // 1 3	

**交换值**

	ES5

	var a = 1;
	var b = 2;
	var tmp = a;
	a = b;
	b = tmp;
	console.log(a, b); // 2 1

等同于：

	ES6

	let a = 1;
	let b = 2;
	[a, b] = [b,a]
	console.log(a, b); // 2 1

**解构多返回值**

	ES5

	function margin() {
	  var left=1, right=2, top=3, bottom=4;
	  return { left: left, right: right, top: top, bottom: bottom };
	}
	var data = margin();
	var left = data.left;
	var bottom = data.bottom;
	console.log(left, bottom); // 1 4

在第三行，你可以返回一个数组想这个样子（同时也可以减少代码量）：

	return [left, right, top, bottom];

但是接下来，调用者需要去考虑数据返回的顺序。

	var left = data[0];
	var top = data[2];

在ES6中，调用者只需要选择他需要的数据即可（如下第6行）:

	ES6

	function margin() {
	  const left=1, right=2, top=3, bottom=4;
	  return { left , right, top ,bottom };
	}
	const {left , bottom} = margin();
	console.log(left, bottom); // 1 4

***注意***：在上面代码第三行中，有一些其他ES6的特性。我们能将`{ left: left}`简化为`{ left }`。
看起来比ES5简洁多了，是不是很屌？

**参数匹配结构**

	ES5

	var user = {firstName: 'Adrian', lastName: 'Mejia'};
	function getFullName(user) {
	  var firstName = user.firstName;
	  var lastName = user.lastName;
	  return firstName + ' ' + lastName;
	}
	console.log(getFullName(user)); // Adrian Mejia

等同于（但是更简洁）：

	ES6

	const user = {firstName: 'Adrian', lastName: 'Mejia'};
	function getFullName({firstName, lastName}) {
	  return `${firstName} ${lastName}`;
	}
	console.log(getFullName(user)); // Adrian Mejia

**深度匹配**

	ES5

	function settings() {
	  return { display: { color: 'red' }, keyboard: { layout: 'querty'} };
	}
	var tmp = settings();
	var displayColor = tmp.display.color;
	var keyboardLayout = tmp.keyboard.layout;
	console.log(displayColor, keyboardLayout); // red querty

等同于（但是更加简洁）：

	ES6

	function settings() {
	  return { display: { color: 'red' }, keyboard: { layout: 'querty'} };
	}
	const { display: { color: displayColor }, keyboard: { layout: keyboardLayout }} = settings();
	console.log(displayColor, keyboardLayout); // red querty

这也称为对象（Object）解构。
如你所见，解构是非常有用的，同时也鼓励了良好的编码风格。

> 最佳实践：

> * 使用数组结构去获取元素和交换变量。这样会避免去创建临时引用。
> * 不要使用数组结构去返回复杂数据结构，而是要使用对象解构。

## 3.5 类和对象

ES6中, 我们从 “构造函数” 🔨 来到了 “类” 🍸.

> 在JavaScript中每一个对象都有一个原型对象。所有的JavaScript对象都从他的原型对象哪里继承了方法和属性。

在ES5中，我们实现面向对象编程，使用构造函数去创建对象如下：

	ES5

	var Animal = (function () {
	  function MyConstructor(name) {
	    this.name = name;
	  }
	  MyConstructor.prototype.speak = function speak() {
	    console.log(this.name + ' makes a noise.');
	  };
	  return MyConstructor;
	})();
	var animal = new Animal('animal');
	animal.speak(); // animal makes a noise.

在ES6中，我们有一些语法糖。我们可以使用`class`,`constructor`来实现同样的事，同时减少样板代码。
同时注意下，我们定义方法的简洁性：

`constructor.prototype.speak = function ()` vs `speak()`:

	ES6

	class Animal {
	  constructor(name) {
	    this.name = name;
	  }
	  speak() {
	    console.log(this.name + ' makes a noise.');
	  }
	}
	const animal = new Animal('animal');
	animal.speak(); // animal makes a noise.

正如我们所见，ES5，ES6这两种实现方式，最终的代码运行结果是一样的并且创建的类的用法也相同。

> 最佳实践
> * 总是使用`class`语法糖，避免直接操作`proptotype`。为啥？？？因为这样让代码变的更加简洁和易懂。
> * 避免使用一个空的构造函数。如果没有指定构造函数，类会有一个默认构造函数。

## 3.6 继承

基于前面的`Animal`类，假设我们想要扩展它定义一个`Lion`类。

在ES5中，这是一个涉及到原型继承。

	ES5

	var Lion = (function () {
	  function MyConstructor(name){
	    Animal.call(this, name);
	  }
	  // prototypal inheritance
	  MyConstructor.prototype = Object.create(Animal.prototype);
	  MyConstructor.prototype.constructor = Animal;
	  MyConstructor.prototype.speak = function speak() {
	    Animal.prototype.speak.call(this);
	    console.log(this.name + ' roars');
	  };
	  return MyConstructor;
	})();
	var lion = new Lion('Simba');
	lion.speak(); // Simba makes a noise.
	// Simba roars.

我不会重复所有的细节，但是注意一下几点：

* 第3行：我们显示的调用了`Animal`的带参构造函数。
* 第7-8行：我们将`Lion`的原型指向了`Animal`的原型。
* 第11行：我们调用了父类`Animal`的`speak`方法。

在ES6中，我们有了`extends`和`super`新关键字。

	ES6

	class Lion extends Animal {
	  speak() {
	    super.speak();
	    console.log(this.name + ' roars');
	  }
	}
	const lion = new Lion('Simba');
	lion.speak(); // Simba makes a noise.
	// Simba roars.

对比ES5和ES6的代码，虽然他们做了相同的事情，但是ES6要清晰简洁的多。ES6 Win！！！

> 最佳实践：
> * 使用`extends`内置方法实现继承。

3.7 原生Promises

从回调地狱 👹 到Promises 🙏

	ES5

	function printAfterTimeout(string, timeout, done){
	  setTimeout(function(){
	    done(string);
	  }, timeout);
	}
	printAfterTimeout('Hello ', 2e3, function(result){
	  console.log(result);
	  // nested callback
	  printAfterTimeout(result + 'Reader', 2e3, function(result){
	    console.log(result);
	  });
	});

我们有一个接受回调函数的function，当done时执行回调。我们必须一个接一个的执行它两次。这也是我们调用了`printAfterTimeout`两次的原因。

如果你需要3次或者4次回调的时候,你的代码将会变的脏乱差。让我们看看如何用Promises实现它：

	ES6

	function printAfterTimeout(string, timeout){
	  return new Promise((resolve, reject) => {
	    setTimeout(function(){
	      resolve(string);
	    }, timeout);
	  });
	}
	printAfterTimeout('Hello ', 2e3).then((result) => {
	  console.log(result);
	  return printAfterTimeout(result + 'Reader', 2e3);
	}).then((result) => {
	  console.log(result);
	});

如你所见，使用`Promises`后，我们能使用`then`在函数完成后进行一些操作，不在需要嵌套函数。

## 3.8 箭头函数

ES6没有移除函数表达式但是它增加了箭头函数表达式。

在ES5中，我们对`this`的使用有一些问题:

	ES5

	var _this = this; // need to hold a reference
	$('.btn').click(function(event){
	  _this.sendData(); // reference outer this
	});

你需要使用一个临时的`this`，在函数内部进行引用，在ES6中你可以使用箭头函数：

	ES6

	// ES6
	// 引用的是外部的那个 this
	$('.btn').click((event) =>  this.sendData());

## 3.9 For...of

从`for`到`forEach`再到`For...of`:

	ES5

	// for
	var array = ['a', 'b', 'c', 'd'];
	for (var i = 0; i < array.length; i++) {
	  var element = array[i];
	  console.log(element);
	}
	// forEach
	array.forEach(function (element) {
	  console.log(element);
	});

ES6的For...of 同样可以实现迭代：

	ES6

	// for ...of
	const array = ['a', 'b', 'c', 'd'];
	for (const element of array) {
	    console.log(element);
	}

## 3.10 默认参数

我们从检验一个变量是否定义到分配一个值再到默认参数。你之前有木有写过类似的代码？

	ES5

	function point(x, y, isFlag){
	  x = x || 0;
	  y = y || -1;
	  isFlag = isFlag || true;
	  console.log(x,y, isFlag);
	}
	point(0, 0) // 0 -1 true 😱
	point(0, 0, false) // 0 -1 true 😱😱
	point(1) // 1 -1 true
	point() // 0 -1 true

可能有吧，这是检验变量或者分配默认值的常见模式。然而，注意这里有一些问题：

* 第8行：我们传入的`0,0`，但是得到是`0,-1`。
* 第9行：我们传入的`false`,但是得到的是`true`。

如果你的默认参数是boolean值或者设置值为0，那么就不起作用了。你晓得为啥子嘛？？ES6的栗子举完了再告诉你，科科。

ES6，你能用更少的代码做的更屌！

	ES6

	function point(x = 0, y = -1, isFlag = true){
	  console.log(x,y, isFlag);
	}
	point(0, 0) // 0 0 true
	point(0, 0, false) // 0 0 false
	point(1) // 1 -1 true
	point() // 0 -1 true

注意到5,6行没有，我们得到了我们想要的结果了。ES5的栗子却不起作用。我们必须先验证`undefined`，因为`false`,`null`,`undefined`和`0`都是假值。我们可以避开这些问题：

	ES5

	function point(x, y, isFlag){
	  x = x || 0;
	  y = typeof(y) === 'undefined' ? -1 : y;
	  isFlag = typeof(isFlag) === 'undefined' ? true : isFlag;
	  console.log(x,y, isFlag);
	}
	point(0, 0) // 0 0 true
	point(0, 0, false) // 0 0 false
	point(1) // 1 -1 true
	point() // 0 -1 true

当我们先检验了`undefined`后运行结果和我们预期一样了。

## 3.11 剩余参数

从参数到剩余参数再到扩展操作符。

在ES5中，获取任意数量的参数是非常麻烦的。

	ES5

	function printf(format) {
	  var params = [].slice.call(arguments, 1);
	  console.log('params: ', params);
	  console.log('format: ', format);
	}
	printf('%s %d %.2f', 'adrian', 321, Math.PI);

我们能用剩余参数`...`做同样的事：

	ES6

	function printf(format, ...params) {
	  console.log('params: ', params);
	  console.log('format: ', format);
	}
	printf('%s %d %.2f', 'adrian', 321, Math.PI);

## 3.12 扩展运算符

从`apply()`到扩展运算符。同样我们也能用`...`解决：

> 提醒：我们使用`apply()`将数组转换为一列参数。例如：`Math.max()`是作用于一列参数，但是如果我们有一个数组，我们能使用`apply()`让它生效。

![java-javascript](/img/in-post/ES6-features-translate/javaScript-Math-apply-arrays.png)

正如上显示，我们能使用`apply()`将数组转换为参数。

	ES5

	Math.max.apply(Math, [2,100,1,6,43]) // 100

在ES6中，你能使用扩展运算符：

	ES6

	Math.max(...[2,100,1,6,43]) // 100

当然，从`concat`数组到使用扩展运算符：

	ES5

	var array1 = [2,100,1,6,43];
	var array2 = ['a', 'b', 'c', 'd'];
	var array3 = [false, true, null, undefined];
	console.log(array1.concat(array2, array3));

在ES6中，你可以使用扩展运算符压平嵌套。

	ES6

	const array1 = [2,100,1,6,43];
	const array2 = ['a', 'b', 'c', 'd'];
	const array3 = [false, true, null, undefined];
	console.log([...array1, ...array2, ...array3]);

# 4. 总结

JavaScript经历了相当多的改变。这篇文章涵盖了每个JavaScript开发者都应该知道的大多数核心特征。同时，我们也介绍了一些让你的代码更加简洁，易懂的最佳实践。

如果你认为还有一些没有提到的**核心**的功能，请在下方留言，我会更新这篇文章。

[*原文地址*](http://adrianmejia.com/blog/2016/10/19/Overview-of-JavaScript-ES6-features-a-k-a-ECMAScript-6-and-ES2015/)