---
layout: single
title: 理解Javascript中的this
---

有关javascript难懂概念的第一篇：有关this，摘自IBM Works

在 Java 等面向对象的语言中，this 关键字的含义是明确且具体的，即指代当前对象。一般在编译期确定下来，或称为编译期绑定。而在 JavaScript 中，this 是动态绑定，或称为运行期绑定的，这就导致 JavaScript 中的 this 关键字有能力具备多重含义，带来灵活性的同时，也为初学者带来不少困惑。本文仅就这一问题展开讨论，阅罢本文，读者若能正确回答 JavaScript 中的 What ’s this 问题，作为作者，我就会觉得花费这么多功夫，撰写这样一篇文章是值得的。

## JavaScript 语言中的 this

由于其运行期绑定的特性，JavaScript 中的 this 含义要丰富得多，它可以是全局对象、当前对象或者任意对象，这完全取决于函数的调用方式。JavaScript 中函数的调用有以下几种方式：作为对象方法调用，作为函数调用，作为构造函数调用，和使用 apply 或 call 调用。下面我们将按照调用方式的不同，分别讨论 this 的含义。

### 作为对象方法调用

在 JavaScript 中，函数也是对象，因此函数可以作为一个对象的属性，此时该函数被称为该对象的方法，在使用这种调用方式时，this 被自然绑定到该对象。

##### 清单 2. point.js
```Javascript
var point = { 
	x : 0, 
	y : 0, 
	moveTo : function(x, y) { 
		this.x = this.x + x; 
		this.y = this.y + y; 
	} 
}; 
point.moveTo(1, 1)//this 绑定到当前对象，即 point 对象`
```
### 作为函数调用

函数也可以直接被调用，此时 this 绑定到全局对象。在浏览器中，window 就是该全局对象。比如下面的例子：函数被调用时，this 被绑定到全局对象，接下来执行赋值语句，相当于隐式的声明了一个全局变量，这显然不是调用者希望的。

##### 清单 3. nonsense.js

```javascript
function makeNoSense(x) { 
	this.x = x;
} 
makeNoSense(5);
x;
// x 已经成为一个值为 5 的全局变量
```

对于内部函数，即声明在另外一个函数体内的函数，这种绑定到全局对象的方式会产生另外一个问题。我们仍然以前面提到的 point 对象为例，这次我们希望在 moveTo 方法内定义两个函数，分别将 x，y 坐标进行平移。结果可能出乎大家意料，不仅 point 对象没有移动，反而多出两个全局变量 x，y。

##### 清单 4. point.js
```javascript
var point = { 
	x : 0, 
	y : 0, 
	moveTo : function(x, y) { 
		// 内部函数
		var moveX = function(x) {
		this.x = x;//this 绑定到了哪里？
		}; 
		// 内部函数
		var moveY = function(y) { 
			this.y = y;//this 绑定到了哪里？
		}; 
		moveX(x); 
		moveY(y); 
	} 
}; 
point.moveTo(1, 1);
point.x; //==>0
point.y; //==>0 
x; //==>1
y; //==>1
```
这属于 JavaScript 的设计缺陷，正确的设计方式是内部函数的 this 应该绑定到其外层函数对应的对象上，为了规避这一设计缺陷，聪明的 JavaScript 程序员想出了变量替代的方法，约定俗成，该变量一般被命名为 that。

##### 清单 5. point2.js
```javascript
var point = { 
	x : 0, 
	y : 0, 
	moveTo : function(x, y) {
		var that = this; 
		// 内部函数
		var moveX = function(x) {
			that.x = x; 
		}; 
		// 内部函数
		var moveY = function(y) {
			that.y = y;
		} 
		moveX(x); 
		moveY(y); 
	}
}; 
point.moveTo(1, 1);
point.x; //==>1 
point.y; //==>1
```
### 作为构造函数调用

JavaScript 支持面向对象式编程，与主流的面向对象式编程语言不同，JavaScript 并没有类（class）的概念，而是使用基于原型（prototype）的继承方式。相应的，JavaScript 中的构造函数也很特殊，如果不使用 new 调用，则和普通函数一样。作为又一项约定俗成的准则，构造函数以大写字母开头，提醒调用者使用正确的方式调用。如果调用正确，this 绑定到新创建的对象上。

##### 清单 6. Point.js
```javascript
function Point(x, y){
	this.x = x;
	this.y = y;
}
```
### 使用 apply 或 call 调用

让我们再一次重申，在 JavaScript 中函数也是对象，对象则有方法，apply 和 call 就是函数对象的方法。这两个方法异常强大，他们允许切换函数执行的上下文环境（context），即 this 绑定的对象。很多 JavaScript 中的技巧以及类库都用到了该方法。让我们看一个具体的例子：

##### 清单 7. Point2.js

```javascript
function Point(x, y){
	this.x = x; 
	this.y = y;
	this.moveTo = function(x, y){
		this.x = x;
		this.y = y;
	}
}
var p1 = new Point(0, 0);
var p2 = {x: 0, y: 0};
p1.moveTo(1, 1);
p1.moveTo.apply(p2, [10, 10]);
```

在上面的例子中，我们使用构造函数生成了一个对象 p1，该对象同时具有 moveTo 方法；使用对象字面量创建了另一个对象 p2，我们看到使用 apply 可以将 p1 的方法应用到 p2 上，这时候 this 也被绑定到对象 p2 上。另一个方法 call 也具备同样功能，不同的是最后的参数不是作为一个数组统一传入，而是分开传入的。

### 换个角度理解

如果像作者一样，大家也觉得上述四种方式不方便记忆，过一段时间后，又搞不明白 this 究竟指什么。那么我向大家推荐 Yehuda Katz 的这篇文章：[Understanding JavaScript Function Invocation and “this”](http://yehudakatz.com/2011/08/11/understanding-javascript-function-invocation-and-this/)。在这篇文章里，Yehuda Katz 将 apply 或 call 方式作为函数调用的基本方式，其他几种方式都是在这一基础上的演变，或称之为语法糖。Yehuda Katz 强调了函数调用时 this 绑定的过程，不管函数以何种方式调用，均需完成这一绑定过程，不同的是，作为函数调用时，this 绑定到全局对象；作为方法调用时，this 绑定到该方法所属的对象。

### 结束？

通过上面的描述，如果大家已经能明确区分各种情况下 this 的含义，这篇文章的目标就已经完成了。如果大家的好奇心再强一点，想知道为什么 this 在 JavaScript 中的含义如此丰富，那就得继续阅读下面的内容了。作者需要提前告知大家，下面的内容会比前面稍显枯燥，如果只想明白 this 的含义，阅读到此已经足够了。如果大家不嫌枯燥，非要探寻其中究竟，那就一起迈入下一节吧。

## 函数的执行环境

JavaScript 中的函数既可以被当作普通函数执行，也可以作为对象的方法执行，这是导致 this 含义如此丰富的主要原因。一个函数被执行时，会创建一个执行环境（ExecutionContext），函数的所有的行为均发生在此执行环境中，构建该执行环境时，JavaScript 首先会创建 `arguments`变量，其中包含调用函数时传入的参数。接下来创建作用域链。然后初始化变量，首先初始化函数的形参表，值为 `arguments`变量中对应的值，如果 `arguments`变量中没有对应值，则该形参初始化为 `undefined`。如果该函数中含有内部函数，则初始化这些内部函数。如果没有，继续初始化该函数内定义的局部变量，需要注意的是此时这些变量初始化为 `undefined`，其赋值操作在执行环境（ExecutionContext）创建成功后，函数执行时才会执行，这点对于我们理解 JavaScript 中的变量作用域非常重要，鉴于篇幅，我们先不在这里讨论这个话题。最后为 `this`变量赋值，如前所述，会根据函数调用方式的不同，赋给 `this`全局对象，当前对象等。至此函数的执行环境（ExecutionContext）创建成功，函数开始逐行执行，所需变量均从之前构建好的执行环境（ExecutionContext）中读取。

### Function.bind

有了前面对于函数执行环境的描述，我们来看看 this 在 JavaScript 中经常被误用的一种情况：回调函数。JavaScript 支持函数式编程，函数属于一级对象，可以作为参数被传递。请看下面的例子 myObject.handler 作为回调函数，会在 onclick 事件被触发时调用，但此时，该函数已经在另外一个执行环境（ExecutionContext）中执行了，this 自然也不会绑定到 myObject 对象上。

##### 清单 8. callback.js

`button.onclick = myObject.handler;`

这是 JavaScript 新手们经常犯的一个错误，为了避免这种错误，许多 JavaScript 框架都提供了手动绑定 this 的方法。比如 Dojo 就提供了 lang.hitch，该方法接受一个对象和函数作为参数，返回一个新函数，执行时 this 绑定到传入的对象上。使用 Dojo，可以将上面的例子改为：

##### 清单 9. Callback2.js

`button.onclick = lang.hitch(myObject, myObject.handler);`

在新版的 JavaScript 中，已经提供了内置的 bind 方法供大家使用。

### eval 方法

JavaScript 中的 eval 方法可以将字符串转换为 JavaScript 代码，使用 eval 方法时，this 指向哪里呢？答案很简单，看谁在调用 eval 方法，调用者的执行环境（ExecutionContext）中的 this 就被 eval 方法继承下来了。

## 结束语

本文介绍了 JavaScript 中的 this 关键字在各种情况下的含义，虽然这只是 JavaScript 中一个很小的概念，但借此我们可以深入了解 JavaScript 中函数的执行环境，而这是理解闭包等其他概念的基础。掌握了这些概念，才能充分发挥 JavaScript 的特点，才会发现 JavaScript 语言特性的强大。

**datagridview**