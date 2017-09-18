---
layout:     post
title:      "Chapter 1: About This"
subtitle:   " \"You Don't Know JS: this & Object Prototypes\""
date:       2017-08-19 17:19:00
author:     "Wolfdu"
header-img: "img/in-post/ydkjs/JavaScript-logo.png"
catalog:    true
tags:
    - JavaScript
---

> 本文为You don't know JavaScript学习笔记

# 1.关于this

this关键字是JavaScript中最为复杂的机制之一。他会被自动定义在所有函数作用域中。

想要要说清楚this具体指向什么，却毫无头绪。

## 1.1 为什么要用this？

对于有经验的JavaScript开发者来说this都是一种复杂的机制，那么他到底用在哪里呢？
是否值得我们花精力去搞清楚它吗？所以我们得知道为什么要学习this？

下面我们来解释为什么要使用this：

    function identify() {
        return this.name.toUpperCase();
    }

    function speak() {
        var greeting = 'Hello, I'm ' + identify.call(this);
        console.log(greeting);
    }

    var me = {
        name: 'Kyle'
    };

    var you = {
        name: 'Reader'
    };

    identify.call(me);//KYLE
    identify.call(you);//READER

    speak.call(me);//Hello, I'm KYLE
    speak.call(you);//Hello, I'm READER

这段代码可以在不同上下文对象（me 和 you）中重复的使用函数`identify()`和`speak()`
不用针对每个对象编写不同版本的函数，如果不使用this，那么就需要给这两函数显示的传入
一个上下文对象。

随着使用的的模式越来越复杂，显示传递的上下文对象会让代码变的越来越混乱，使用this则不
会这样。this提供的了一种更加优雅的隐式的传递方式，因此可以将API设计的更加简洁。

## 1.2 误解

由于this的字面意识，导致了我们对this的理解产生了一些偏差。常见的误解有两种。

### 1.2.1 指向本身

我们很容易将this理解为，函数自身并且从字面理解也是说的通的。

我们直接上荔枝来验证一下这个说法吧

    function foo(num){
        console.log('foo: '+ num);

        //记录foo被调用次数
        this.count++
    }

    foo.count = 0;

    for(var i =0;i<10;i++){
        if(i>5){
            foo(i);
        }
    }

    //foo: 6
    //foo: 7
    //foo: 8
    //foo: 9

    console.log(foo.count);//0-->wtf

从结果来看foo被调用了4次为什么count是0 呢？

由此可见，this并不是如我们所想指向函数本身的。
不妨深入思考下，为什么count属性值和预期结果不一样？count绑定到哪里了呢？

如果我们不深入思考this，我们会用其他方式回避他。

如使用词法作用域创建另一个带有count属性的对象来解决这个问题。

    function foo(num){
        console.log('foo'+ num);

        //记录foo被调用次数
        data.count++
    }

    var data = {
        count: 0
    };

    for(var i =0;i<10;i++){
        if(i>5){
            foo(i);
        }
    }

    //foo6
    //foo7
    //foo8
    //foo9

    console.log(data.count);//4

> 这个办法解决了问题，但是也回避了真正的问题。

如果我们要在函数内部引用函数自身怎么办呢？

一般来说只使用this是不够的你需要一个指向函数对象的词法标识符来引用他（具名函数）：

    function foo(num){
        console.log('foo'+ num);

        //记录foo被调用次数
        foo.count++
    }

    foo.count = 0;

    for(var i =0;i<10;i++){
        if(i>5){
            foo(i);
        }
    }

    //foo6
    //foo7
    //foo8
    //foo9

    console.log(foo.count);//4

> 然而，这样的方式同样是回避了this的问题，依赖于foo的词法作用域解决了问题。

还有一种方式是，强制this指向foo函数对象：

    function foo(num){
        console.log('foo'+ num);

        //记录foo被调用次数，这里的this实际指向foo
        this.count++
    }

    foo.count = 0;

    for(var i =0;i<10;i++){
        if(i>5){
           foo.call(foo,i);
        }
    }
    //foo6
    //foo7
    //foo8
    //foo9

    console.log(foo.count);//4

这一次我们拥抱了this，但是其中还有很多困惑，后面我们在详细学习其中原理。

### 1.2.2 指向作用域

第二种常见的误解是this指向函数的作用域。这样问题就有点复杂了，因为通常情况下这样理
解是正确的，但是在一些情况下他是错误的。

这里可以明确一点，this在任何情况下都不指向函数的词法作用域。作用域确实和对象类似但是
作用域对象是无法通过JavaScript代码访问的，他存在于JavaScript引擎内部。

思考如下代码，他试图跨越边界，使用this来隐式的引用函数的词法作用域。

    function foo(){
        var a = 1;
        this.bar();
    }

    function bar(){
        console.log(this.a);
    }

    foo();//undefined

首先，这段代码试图通过this.bar()来引用bar()函数。但是这样能够调用成功也是一个意外
，之后会明白。我们都知道最为自然的调用方式是通过词法引用标识符。

此外，这段代码还试图通过this来联通foo()和bar()的词法作用域，从而让bar()访问到foo()
作用域中的变量a。实际上使用this在词法作用域是查找不到的。

所以当你想要把this和此法作用关联起来的时候，你应该注意到，这是不可行的。

## 1.3 this到底是什么？

在排除了以上错误理解之后，我们来探一探究竟，this到底是一种什么样的机制。

this是在运行时进行绑定的，并不是在编写是绑定的，他的上下文取决于函数调用时的各种条件
。this的绑定和函数的声明位置没有任何关系，值取决于函数的调用方式。

当一个函数被调用的时候，会创建一个活动记录（执行上下文）。这个上下文会包含，函数调用
位置（调用栈），函数调用方式，传入参数等信息。this就是这个上下文的一个属性。

## 1.4 小结

对于学习this机制到底有没有意义？

this绑定一直是一件非常复杂并且令人困惑的事，但是他非常的重要，并且依靠猜测，试错，和
回避来解决这些问题是很不明智的。

明白this即不是指向函数本身也不是指向函数作用域。

this是在运行过程中进行绑定的，与执行上下文有关。





