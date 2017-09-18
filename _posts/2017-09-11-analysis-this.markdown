---
layout:     post
title:      "Chapter 2: Analysis This"
subtitle:   " \"You Don't Know JS: this & Object Prototypes\""
date:       2017-09-18 22:19:00
author:     "Wolfdu"
header-img: "img/in-post/ydkjs/JavaScript-logo.png"
catalog:    true
tags:
    - JavaScript
---

> 本文为You don't know JavaScript学习笔记

# 1. 详解this

在上一个章节中我们了解到关于this的错误理解，并且知道了函数中的this是调用时绑定的，
与函数编写位置无关与调用位置有关。

## 1.1 调用位置

**调用位置**字面上理解即为函数在代码中调用的位置（注意区别声明位置）。

要找到函数的调用位置我们就要分析*调用栈*(代码当前执行位置的所有调用函数)。

下面我们用一段代码来看看什么是调用位置和调用栈：

    function foo(){
        //当前调用栈是：baz-->bar-->foo
        //因此当前滴啊用位置在bar中

        console.log('foo');
    }

    function bar(){
        //当前调用栈是:baz-->bar
        //当前调用位置：baz中

        console.log('bar');
        foo();//<-- foo调用位置
    }

    function baz(){
        //当前调用栈是：baz
        //当前调用位置是全局作用域

        console.log('baz');
        bar();//<--bar调用位置
    }

    baz();//<--baz调用位置


由此我们可以找到函数的调用位置，其实在chrome的调试工具中，我们可以通过断点找到当前的
调用栈，函数调用位置以及this等信息。找到了函数的调用位置，我们也找到了决定this的关键因素。

## 1.2 绑定规则

在找到函数调用位置之后我们要判断，this的绑定适用于什么样的规则，这里有4条绑定规则。

### 1.2.1 默认绑定

我们从最简单也是最常见的调用类型开始：独立函数调用。

当this绑定不适用于其他几种方法的时候默认使用这种绑定规则。

分析如下代码：

    function foo(){
        console.log(this.a);
    }

    var a = 2;

    foo();//2

首先通过foo()的调用位置可我们以发现foo是直接使用函数引用进行调用的，应用了this的默认绑定，
因此this指向全局对象。

如果使用严格模式（strict mode），则不能将全局变量绑定到this，因此this会绑定到undefined：

    function foo(){
        'use strict';
        console.log(this.a);
    }

    var a = 2;

    foo();//TypeError: Cannot read property 'a' of undefined

> 通常来说我们不应该在代码中混合使用严格模式和非严格模式

### 1.2.2 隐式绑定

该绑定规则需要考虑调用位置是否有上下文对象。

分析如下代码：

    function foo(){
        console.log(this.a);
    }

    var obj = {
        a:2,
        foo: foo
    };

    obj.foo();//2

> 注意这里foo()的声明方式，foo被当做属性添加到obj中，但是无论是直接在obj中定义
还是先定义再添加到obj中引用为属性，这个函严格来讲都不属于obj对象。

从调用位置来讲，函数使用obj上下文来引用foo，当函数引用有上下文对象时，隐式绑定规则
会把函数调用的this绑定到这个上下文对象上。因此就有了如上结果输出。

另外，对象属性引用链只有最后一层调用位置起作用：

    function foo(){
        console.log(this.a);
    }

    var obj2 = {
        a:2,
        foo: foo
    };

    var obj1 = {
        a:1,
        obj2: obj2
    };

    obj1.obj2.foo();//2

**隐式丢失**

隐式绑定的函数丢失绑定对象是非常常见的，也就是说丢失后会进行默认绑定，从而将this绑定到
全局对象或者undefined上。

分析如下代码：

    function foo() {
        console.log(this.a);
    }

    var obj = {
        a: 2,
        foo: foo
    };

    var bar = obj.foo;//函数别名

    var a = 'oops global';//a是全局对象的属性

    bar();//‘oops global’

代码上看bar是obj.foo的引用但是实际上他引用的是foo函数的本身，因此这里就可以理解为
bar函数的调用没有任何的修饰，即this的绑定规则为默认绑定。

还有另外一种隐式丢失的情况更加的隐蔽：

    function foo() {
        console.log(this.a);
    }

    function doFun(fun){
        fun();//<--调用位置，调用时其实调用的是foo
    }

    var obj = {
        a: 2,
        foo: foo
    };

    var a = 'oops global';

    doFun(obj.foo);//‘oops global’

这里的参数的传递其实就是一种隐式的赋值，所以结果相同，出现了丢失上下文的情况，进行了默认绑定。

> 我们发下回调函数丢失this绑定的情况非常常见，此外还有一种情况在很多JavaScript库中比较常见，
就是事件处理器会将回调函数的this强制的绑定为触发事件的DOM元素。

无论那种情况，暂时看来this的绑定都是不可控的，你无法控制回调函数的执行方式，也就没有办法控制
调用位置以得到期望的绑定。后面我们会学习到如何固定this来解决这个问题。

### 1.2.3 显示绑定

在上一小节中我们必须在一个对象中包含一个指向函数的属性，间接的调用函数，才能将this隐式的绑定
到这个对象上。那我们如何直接将函数的this绑定到我们指定的对象上呢？

JavaScript中绝大部分的函数包括自己定义的函数都有call(...),apply(...)方法，让我们显示的绑定this。

简单介绍一下这两方法是如何工作的，他们的第一个参数是一个对象，是为this准备的，在调用函数时将其绑定到this。
这样你就可以直接指定this的绑定对象了。

分析如下代码：

    function foo(){
        console.log(this.a);
    }

    var obj ={
        a:2
    };

    foo.call(obj);//2

我们可以发现，通过函数调用call方法将obj绑定到了this。

> 从this绑定的角度，call(...)和apply(...)是一样的，他们的区别体现在参数上。

但是显示绑定任然无法解决丢失绑定的情况。

#### 1.2.3.1 硬绑定

但是这种显示绑定的变化形式却可以实现。

分析如下代码：

    function foo(){
        console.log(this.a);
    }

    var obj = {
        a:2
    };

    var bar = function(){
        foo.call(obj);
    };

    bar();//2
    setTimeout(bar,100);//2

    //硬绑定的bar不可能再修改它的this
    bar.call(window);//2

我们在bar的内部将foo的this显示的绑定到obj，这样无论怎么调用bar，总会在obj上调用foo，这种形式叫做硬绑定。

硬绑定的典型应用场景就是创建一个包裹函数：

    function foo(something){
        console.log(this.a,something);
        return this.a+something;
    }

    var obj = {
        a:2
    };

    var bar = function(){
      return foo.apply(obj,arguments);
    };

    var b = bar(3);//2 3
    console.log(b);//5

另外一种场景，创建一个可以重复使用的辅助函数：

    function bind(fn,obj){
        return function(){
            return fn.apply(obj,arguments);
        }
    }

    function foo(something){
        console.log(this.a);
        return this.a + something;
    }

    var obj = {
        a:2
    };

    var bar = bind(foo,obj);

    var b = bar(3);//2 3
    console.log(b);//5

由于硬绑定非常常用，所以ES5提供了内置方法Function.prototype.bind 用法如下:

    function foo(something){
        console.log(this.a);
        return this.a + something;
    }

    var obj = {
        a:2
    };

    var bar = foo.bind(obj);

    var b = bar(3);//2 3
    console.log(b);//5

bind(...)会返回一个硬编码的新函数，会将指定的参数设置为this的上下文，并调用原始函数。

#### 1.2.3.2 API中是“上下文”

在很多第三方库函数中都会提供一个可选参数，可以指定上下文，其作用和bind(...)是一样的确保回调函数使用指定的this。

例子：

    function foo(something){
        console.log(something,this.a);
    }

    var obj = {
        a:'oh!'
    };

    //指定this-->obj
    [1,2,3].forEach(foo,obj);//1 oh! 2 oh! 3 oh!

这些实际上也是通过call(...),apply(...)实现了显示的绑定，这样可以少写一些代码P:

### 1.2.4 new绑定

在解释new绑定之前，我们先来了解JavaScript中的构造函数。

我们常见的形式是`var num = new Number(...)`,但是这和我们常见的面向对象类语言如Java中构造函数是完全不同的。
JavaScript中构造函数只是使用new操作符时被调用的普通函数，他不属于某一个类，也不会实例化一个类。

实际上使用new调用函数时会发生如下操作：

1. 创建一个全新的对象；
2. 这个新对象会被执行[Prototype]连接；
3. 这个新对象会绑定到调用函数的this；
4. 如果函数没有其他返回对象，那么new表达式中的函数会自动返回这个新对象。

这里主要关心1,3,4步骤，2我们后面会详细学习。

分析如下代码：

    function foo(a){
        this.a = a;
    }

    var bar = new foo(2);

    console.log(bar.a);//a

通过new来调用foo时，将会构造一个新对象并把他绑定到foo调用的this上，这种形式的this绑定就是--new绑定。

## 1.3 优先级

以上就是函数调用this绑定的4条规则，你需要做的就是找到函数调用位置并判断适用于那一条规则。

but。。。当在某一个调用位置适用于多条规则的时候我们该如何判断呢？

这里就要考虑各个规则之间的优先级了。也是我们接下来学习的内容。

我们首先找出一个最低优先级，当然是默认优先级啦～:P

**隐式绑定 VS 显示绑定**

    function foo(){
        console.log(this.a);
    }

    var obj1 = {
        a:2,
        foo:foo
    };

    var obj2 = {
        a:3,
        foo:foo
    };

    obj1.foo();//2
    obj2.foo();//3

    obj1.foo.call(obj2);//3
    obj2.foo.call(obj1);//2

我们可以清楚看到显示绑定优先级更高，所以在判断时应该优先考虑显示绑定。

**new绑定 VS 隐式绑定**

    function foo(something){
        this.a = something;
    }

    var obj1 = {
        foo:foo
    };

    var obj2 = {};

    obj1.foo(2);
    console.log(obj1.a);//2

    obj1.foo.call(obj2,3);
    console.log(obj2.a);//3

    var bar = new obj1.foo(4);
    console.log(obj1.a);//2
    console.log(bar.a);//4

可以清晰的发现new绑定比隐式绑定的优先级要高。但是显示绑定和new绑定谁的优先级高呢？

> 由于new和call/apply不能一起使用，因此不能通过`new foo.call(obj1)`来直接测试优先级。
但是我们可以通过硬绑定来测试他们的优先级。

**new绑定 VS 显示绑定（硬绑定）**

我们先回忆一下硬绑定是如何工作的，Function.prototype.bind(...)会创建一个包装函数，这个函数会忽略
他当前的this绑定（无论对象是什么），把我们提供的对象绑定到this上。

这样看起来硬绑定视乎更厉害。。。

荔枝：

    function foo(somethig){
        this.a = somethig;
    }

    var obj1 = {};

    var bar = foo.bind(obj1);
    bar(2);
    console.log(obj1.a);//2

    var baz = new bar(3);
    console.log(obj1.a);//2
    console.log(baz.a);//3

额。。意料之外，bar被硬绑定到obj1上了但是，new bar(3)，并没有照着剧本那样把obj1.a修改为3.
反之new绑定修改了硬绑定的this，我们得到的新对象baz.a的值为3.

一下是我们之前的bind辅助函数：

    function bind(fn,obj){
            return function(){
                return fn.apply(obj,arguments);
            }
    }

实际上在ES5中的bind函数的实现要复杂的多，实际实现中会判定硬绑定函数是否被new调用，如果被new调用
就会使用新创建的this来替换硬绑定的this。

之所以要在new中硬绑定函数，主要的目的是预先设置函数的一些参数，这样在使用new进行初始化的时候就只需要传入其余的参数就可以了。

bind(...)的功能之一就是可以把除第一个参数之外的其他参数传递给下层的函数（称为部分应用，是柯里化的一种）。

    function foo(p1,p2){
        this.val = p1+p2;
    }

    var bar = foo.bind(null,'p1');

    var baz =new bar('p2');
    console.log(baz.val);//p1p2

通过以上的PK我们得到this绑定规则优先级关系，我们就知道了如何判断this的绑定。

1. 函数是否在new中被调用，如果是那么this绑定的新创建的对象。
2. 函数是否通过call/apply或者硬绑定，如果是那么this绑定的是指定的对象。
3. 函数是否在指定的上下文中调用，如果是那么this绑定的是指定的上下文。
4. 如果以上都不是，那么使用默认绑定，在严格模式下就绑定到undefined，非严格模式绑定到全局。

BUT。。。凡事总有例外:P

## 1.4 绑定例外

在默写场景下this的绑定会出其不意：）

### 1.4.1 被忽略的this

如果将null或者undefined作为this绑定对象传入call，apply或者bind函数这些值在调用的时候会被忽略，实际应用的时候执行的是默认绑定规则。

    function foo(){
        console.log(this.a);
    }

    var a = 2;

    foo.call(null);//2

那什么情况会传入null？

使用bind对参数进行柯里化（预先设置一些参数）：

    function foo(p1,p2){
        console.log('p1:'+p1+' p2:'+p2);
    }

    var bar = foo.bind(null,1);
    bar(2);//p1:1 p2:2

这种方法需要传入一个null来占位，因为函数并不关心this的绑定对象。

但是总是通过null来忽略this的绑定是有副作用的，如果某个函数确实使用了this，那么默认绑定会将this绑定到全局上，导致一些难以分析的bug。

所以说我们应该创建一个更加安全的this绑定对象。

就是传入一个特殊对象，将this绑定到这个对象不会对你的程序有任何的副作用--空的非委托对象（委托后面会进行学习）。

你可以使用你喜欢的命名来声明他这里使用DMZ（demilitarized zone，非军事区），创建一个空对象我们使用Object.create(null)，
与{}相较而言这样创建的对象更空，因为这样不会创建Object.prototype这个委托。

    function foo(p1,p2){
        console.log('p1:'+p1+' p2:'+p2);
    }

    var DMZ = Object.create(null);

    var bar = foo.bind(DMZ,1);
    bar(2);

这样就更加的完美了。

### 1.4.2 间接引用

在编码的过程中，你可能会有意无意的创建一个函数的间接引用，这个时候这个函数就会应用默认绑定规则：

    function foo(){
        console.log(this.a);
    }

    var a = 2;
    var o = {
        a:3,
        foo:foo
    };
    var p = {a:4};

    o.foo();//3
    (p.foo = o.foo)();//2

我们前面也提到过，p.foo = o.foo 的返回的是目标函数的引用，因此调用位置是foo而不是p.foo()或者o.foo()所以这里执行默认绑定。

> 另外注意对于默认绑定来说，决定this绑定对象的并不是调用位置是都处于严格模式，而是函数体是否处于严格模式。

### 1.4.3 软绑定

前面我们了解到硬绑定可以强制将this绑定到指定对象（除了new绑定时）。但是这样大大降低了函数的灵活性，绑定了之后就无法修改绑定的this。

如果可以给默认绑定指定一个全局变量或者undefined之外的值，就可以实现和硬绑定相同的效果同时保留隐式绑定和显示绑定修改this的能力。

可以通过如下代码来实现软绑定:

    if (!Function.prototype.softBind) {
    	Function.prototype.softBind = function(obj) {
    		var fn = this,
    			curried = [].slice.call( arguments, 1 ),
    			bound = function bound() {
    				return fn.apply(
    					(!this ||
    						(typeof window !== "undefined" &&
    							this === window) ||
    						(typeof global !== "undefined" &&
    							this === global)
    					) ? obj : this,
    					curried.concat.apply( curried, arguments )
    				);
    			};
    		bound.prototype = Object.create( fn.prototype );
    		return bound;
    	};
    }

接下来，看看效果如何：

    function foo(){
        console.log('name: '+ this.name);
    }

    var obj = {name:'obj'},
        obj2 = {name:'obj2'},
        obj3 = {name:'obj3'};

    var fooObj = foo.softBind(obj);

    fooObj();//name: obj

    obj2.foo = foo.softBind(obj);
    obj2.foo();//name: obj2

    fooObj.call(obj3);//name: obj3

    setTimeout(obj2.foo,10);//name: obj

我们可以发现，我们可以手动的改变this的绑定的对象，应用默认则绑定到了默认设置的obj。

## 1.5 this词法

以上学习的4条规则基本可以涵盖所有正常函数，但是在ES6中有一种函数无法适用这些规则：箭头函数。

箭头函数不适用以上4中规则，他是根据外层作用域来决定this的。

分析如下代码：

    function foo(){
        return () => {
            console.log(this.a);
        };
    }

    var obj1 = {
        a:2
    };

    var obj2 = {
        a:3
    };

    var bar = foo.call(obj1);
    bar.call(obj2);//2 ，而不是3.

在foo内部创建的箭头函数会捕获foo的this，此时this会绑定到obj1，而箭头函数的绑定是无法修改的。

箭头函数常用语回调函数例如：

    function foo(){
        setTimeout(() => {
            console.log(this.a);
        });
    }

    var obj = {
        a:2
    };

    foo.call(obj);//2

箭头函数可以确保函数的this被绑定到指定的对象，此外他还体现了他使用常见的词法作用域取代了传统的this机制。

其实这种用法和我们在ES6之前使用另外一种模式完全一样：

    function foo(){
        var self = this;
        setTimeout(function(){
            console.log(self.a);
        });
    }

    var obj = {
        a:2
    };

    foo.call(obj);//2

具体来说，箭头函数会继承外层函数调用的this绑定，和上面的例子的机制一样。

虽然箭头函数或者我们上面的这种方式都可以取代bind(...)，但是从本质上讲他们是想替代this机制。

在编码的过程中，如果你绝大部分使用的是`self = this`或者箭头函数来否定this机制，或许应该遵循以下其中一种：

1. 只使用词法作用域并完全抛弃错误的this风格的代码。
2. 完全采用this风格，在必要的时候使用bind(...),尽量避免使用`self = this`和箭头函数。

虽然包含两种风格的代码可以正常运行，但是在同一个程序中混用两种风格代码会使代码难以维护，并且也难以编写。

## 小结

如果想要找到函数中this绑定，我们首先需要找到这个函数的调用位置。

接下来通过4中规则进行判断this绑定对象。

ES6中的箭头函数并不适用于这4条规则，而是通过词法作用域来决定this的绑定。

无论使用哪一种编码风格，词法作用域还是this机制，但是要注意保持代码风格的统一。






















