---
layout:     post
title:      "Chapter 5: Scope Closure"
subtitle:   " \"You Don't Know JS: Scope & Closures\""
date:       2017-07-24 21:25:00
author:     "Wolfdu"
header-img: "img/in-post/ydkjs/JavaScript-logo.png"
catalog:    true
tags:
    - JavaScript
---

> 本文为You don't know JavaScript学习笔记

# 1.作用域闭包（Scope Closure）

接下来我们将学习了解JavaScript中非常重要同时又难以理解的知识点：**闭包**。

理解闭包前提:理解词法作用域。

学习前的思考：

> 闭包是什么，自己有明确的定义没有，是否可以用通俗易懂的语言进行解释？:P

> 上一次遇见闭包是什么时候？他长什么样子？


## 1.1 若影若现

闭包的简单定义：

> MDN Closure: A closure is the combination of a function and the lexical environment within which that function was declared.

> 闭包就是一个函数和该函数声明的词法环境的组合。:P

如果看不懂就看这个通俗的吧:

> 当函数可以记住并访问所在的词法作用域时，就产生了闭包，即使函数是在当前词法作用域之外执行的。

分析如下代码：

    function foo(){
        var a = 2;

        function bar(){
            console.log(a);//2
        }

        bar();
    }

    foo();

如上这段代码，基于函数作用域查找规则，函数`bar()`可以访问到外部作用域中变量`a`。

但是这是闭包吗？

根据之前的闭包的定义，好像是又好像不是，总之不是很清晰。

在这里用词法作用域的查找规则解释更为合理，其实词法作用域查找规则
也是闭包非常重要的一部分。

其实上面代码中，函数`bar()`具有一个涵盖`foo()`作用域的闭包。但是通过这种方式并不能很直接
的观察到闭包。

清晰的展示闭包：

    function foo(){
        var a = 2;

        function bar(){
            console.log(a);
        }

        return bar;
    }

    var baz = foo();

    baz();//2 --this is closure

函数`bar()`能够访问`foo()`内部作用域，我们将`bar()`当做一个值类型进行传递。

`foo()`执行后，将返回值赋值给`baz`并调用`baz()`，实际上是通过不同的标识符调用了内部函数`bar()` 。
`bar()`在自己定义的词法作用域之外的地方执行。

通常情况下，`foo()`执行完成后，引擎的垃圾回收器会释放不再使用的内存空间，但是闭包的神奇之处就是
可以阻止这件事的发生，是的`foo()`的内部作用域依然存在没有被回收。

为什么呢？因为`bar()`声明的位置，他涵盖了`foo()`内部作用域的闭包，是的该作用域一直存活，以供
`bar()`在之后的任意时刻使用。`bar()`持有的`foo()`的作用域引用就叫做**闭包**。

## 1.2 无处不在

闭包绝不是无用的学院理论或用来装x的玩具，他在你的日常代码中无处不在。

如下：

    function wait(message){

        setTimeOut(function timer(){
            console.log(message);
        },1000);
    }

    wait('Hello, closure');

将一个内部函数`timer`传递给`setTimeOut(...)`。`timer`具有涵盖`wait(...)`作用域的闭包。

wait执行1000毫秒后，他内部的作用域没有被回收，timer函数依然保有`wait(...)`作用域的闭包。

在jQuery中常见如下代码：

    function setupBot(name,selector){
        $(selector).click(function activator(){
            console.log('Activating :' + name);
        });
    }

    setupBot('Closure Bot','#bot');

本质上，如果将函数当做第一级的值类型并到处传递，你就会看到闭包在这些函数中的应用。
如：定时器，事件监听器，Ajax请求，web workers 或者任何异步任务中，只要使用了回调函数，
实际上就是在使用闭包。

## 1.3 循环和闭包

分析如下代码：

    for(var i=1; i<=5; i++){
        setTimeout(function timer(){
            console.log(i);
        }, i*1000);
    }

会如何输出呢？预期是分别输出1~5，每秒一次共打印5次。

实际上，这段代码会每秒一次频率输出5次6 。

解释下，该循环的终止条件是i不小于等于5也就是i为6的时候。延迟函数会在循环结束后才执行，事实上
定时器设置为`setTimeout(... , 0)`也会是同样的结果，回调函数依然是在循环结束后才被执行。
因此会出现5个6 。

为什么会出现这种请款呢？

因为我们试图假设在循环中的每个迭代都会给自己捕获一个i的副本。但是根据作用域的工作原理，
尽管循环中的函数都是在各个迭代中分别定义的，但是他们都封闭在一个共享的全局作用域中，因此
只有一个i。

因此我们需要闭包来帮助我们：

    for (var i = 1; i <= 5; i++) {
        (function() {
            setTimeout(function timer() {
                console.log(i);
            }, i * 1000);
        }());
    }

还记得IIFE吧，虽然根据闭包的定义，他并不是恰当的荔枝，但是他确实创建了闭包。

但是这样依然达不到我们的要求，虽然每次迭代过程中IIFE都会将创建当前的作用域封闭起来，但是仅仅将
作用域封闭起来是不够的，仔细看看IIFE只是将一个什么都没有的空作用域封闭了起来，所以最终还是
会通过作用域查找的方式找到全局的i。

所以我们需要小小的改动：

    for (var i = 1; i <= 5; i++) {
        (function(j) {
            setTimeout(function timer() {
                console.log(j);
            }, j * 1000);
        }(i));
    }

IIFE不过是个函数，我们可以将i当做参数传递进去，这样每次迭代封闭的作用域中就有了迭代当前的i了。

搞定！！！

## 1.4 块作用域

思考之前循环中的解决方案，每次迭代中IIFE都会创建一个新的作用域。在学习let声明的时候，我们
知道可以用来劫持一个块作用域，并且在这个块作用域中声明一个变量。

那么：

    for(var i=1; i<=5; i++){
        let j = i;
        setTimeout(function timer(){
            console.log(j);
        }, j*1000);
    }

但是这还不能揭示所有的秘密，在for循环的头部使用的let声明有一个特殊的行为，每次迭代都会进行声明
，随后的迭代都会使用上一次迭代结束时的值来初始化这个变量。

    for(let i=1; i<=5; i++){
        setTimeout(function timer(){
            console.log(i);
        }, i*1000);
    }

是不是很牛x。这个功能确实很酸爽。

## 1.5 模块

分析如下代码：

    function CoolModule(){
        var something = 'cool';
        var another = [1, 2, 3];

        function doSomething(){
            console.log(somrthing);
        }

        function doAnother(){
            console.log(another.join('!'));
        }

        return {
            doSomething: doSomething,
            doAnother: doAnother
        };
    }

    var foo = CoolModule();

    foo.doSomething();//cool
    foo.doAnother();1 ! 2 ! 3

最常见的实现模块的方式，这里展示的是其变体。

`CoolModule()`是一个函数，必须通过调用他来创建一个模块实例，如果不知这个外部函数就无法
创建内部作用域和闭包。其中`doSomething()`和`doAnother()`函数具有涵盖模块实例内部作用域
闭包。通过实例对象将其传递到词法作用域外部，我们就可以清晰的观察到闭包存在了。

通过返回一个{key:value ...}的实例对象来保持内部数据变量的隐藏和私有状态。其实可以将这个对象
类型看做这个模块的公共API。

> 模块返回一个实例对象并不是必须的，也可以返回一个内部函数。JQuery就是很好的例子。jQuery和
$标识符就是jQuery模块的公共API，但是他们本身也是都是函数。

模块模式需要由两个必要条件：

1. 必须由外部封闭的函数，该函数至少被调用一次（每次调用都会创建一个新的模块）；
2. 封闭函数必须返回至少一个内部函数，这样内部函数才能在私有作用域中形成闭包，并且可以访问
或者修改私有状态。

前面一个例子中`CoolModule()`叫做独立的模块创建器，可以被任意次调用，每次调用都会生成一个
新的模块实例，但是当我们只需要使用一次的时候，我们可以实现一个单例模式：

    var foo = (function CoolModule(){
        var something = 'cool';
        var another = [1, 2, 3];

        function doSomething(){
            console.log(somrthing);
        }

        function doAnother(){
            console.log(another.join('!'));
        }

        return {
            doSomething: doSomething,
            doAnother: doAnother
        };
    }());

    foo.doSomething();//cool
    foo.doAnother();1 ! 2 ! 3

### 1.5.1 现代模块机制

大多数的模块加载器和管理器的本质是将这种模块定义封装进一个友好的API，这里会简单学习一些
核心概念：

    var MyModule = (function Manager() {
            var modules = {};

            function define(name, deps, impl) {
                for (var i = 0; i < deps.length; i++) {
                    deps[i] = modules[deps[i]];
                }
                modules[name] = impl.apply(impl, deps);
            }

            function get(name) {
                return modules[name];
            }

            return {
                define: define,
                get: get
            };
        }());

这一段代码的核心是`modules[name] = impl.apply(impl, deps);`。
为模块的定义引入了包装函数，可以传入任何的依赖，并将模块的API储存子啊一个根据名字来管理的
模块列表中。

下面展示如何定义模块：

    MyModule.define('bar', [], function() {
            function hello(who) {
                return 'Let me introduce: ' + who;
            }

            return {
                hello: hello
            };
        });

        MyModule.define('foo', ['bar'], function() {
            var hungry = 'hippo';

            function awesome() {
                console.log(bar.hello(hungry).toUpperCase());
            }

            return {
                awesome: awesome
            };
        });

        var bar = MyModule.get('bar');
        var foo = MyModule.get('foo');

        console.log(bar.hello('hippo'));//Let me introduce: hippo
        foo.awesome();//LET ME INTRODUCE: HIPPO

仔细琢磨琢磨这些示例代码并理解闭包的作用。

这就是模块，所以包管理器也没有任何特殊的魔力，只是进行了更为友好的封装而已。

### 1.5.2 未来模块机制

ES6中为模块增加了一级语法支持。在通过模块系统进行加载时，ES6会将文件当作独立的模块来处理。

所以ES6中模块必须被定义在独立的文件中，一个文件一个模块。浏览器引擎可以在导入模块时同步的
加载模块文件。

bar.js

    function hello(){
        return 'Let me introduce:'+who;
    }

    export hello;

foo.js

    import hello from 'bar';

    var hungry = 'hippo';

    function awesome(){
        console.log(hello(hungry).tiUpperCase());
    }

    export a;

baz.js

    module foo from 'foo';
    module bar from 'bar';

    console.log(bar.hello('rhino'));
    foo.awesome();

import可以将一个模块中的一个或者多个API导入到当前的作用域中，并分别绑定到变量上。如
例子中的hello。

module会将整个模块的API导入并绑定到一个变量上如例子中的foo和bar。

export会将当前模块的一个标识符导出为公共API。

模块中的文件内容会被当作好像包含在作用域闭包中一样来处理。和之前的函数闭包模块一样。

## 1.6 小结

闭包无处不在。

当函数可以记住并访问所在的词法作用域，即使函数是在当前词法作用域之外执行的，这时就产生了闭包。

闭包是一个非常强大的工具，可以用多种形式来实现模块。

模块主要有两个特征：

通过调用包装函数创建内部作用域；包装函数返回至少包括一个对内部函数的引用，创建涵盖整个包装函数
内部作用域的闭包。

