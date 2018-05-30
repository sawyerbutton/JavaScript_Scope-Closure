# 认知JavaScript  

作用域和闭包(Scope and Closure)

---

### 作用域

- 什么是作用域？
- 语言的最基础模型之一就是在变量中存储值，并且在稍后取出或修改这些值的能力
- 在变量中存储值和取出值的能力，给程序赋予状态这一概念
- 引入变量之后，这些变量存活在哪里？
- 这些问题需要一组明确定义的规则，它定义如何在某些位置存储变量，以及如何在稍后找到这些变量。AKA：作用域

---
### 理解作用域
- 登场演员
- 引擎：负责从始至终的编译和执行我们的 JavaScript 程序
- 编译器：引擎 的朋友之一；处理所有的解析和代码生成的苦活累活
- 作用域：引擎 的另一个朋友；收集并维护一张所有被声明的标识符（变量）的列表，并对当前执行中的代码如何访问这些变量强制实施一组严格的规则。

---
### What happen for this
```javascript
var a = 2;
```

- 遇到 var a, 编译器 让 作用域 去查看对于这个特定的作用域集合，变量 a 是否已经存在了。如果是，编译器 就忽略这个声明并继续前进。否则，编译器 就让 作用域 去为这个作用域集合声明一个称为 a 的新变量
- 然后 编译器 为 *引擎* 生成稍后要执行的代码，来处理赋值 a = 2, *引擎* 运行的代码首先让 *作用域* 去查看在当前的作用域集合中是否有一个称为 a 的变量可以访问。如果有，*引擎* 就使用这个变量。如果没有，*引擎* 就查看 其他地方

---

- 如果 引擎 最终找到一个变量，它就将值 2 赋予它, 如果没有，引擎 将会举起它的手并喊出一个错误！  
- 总结: 对于一个变量赋值，发生了两个不同的动作：第一，编译器 声明一个变量（如果先前没有在当前作用域中声明过），第二，当执行时，引擎 在 作用域 中查询这个变量并给它赋值，如果找到的话

---
### 编译器术语
- LRS(Left-hand Side)  当一个变量出现在赋值操作的左手边时，会进行 LHS 查询
- RHS(Right-hand Side)  当一个变量出现在赋值操作的右手边时，会进行 RHS 查询

---
### Example 
```javascript
console.log( a );
```

- 指向 a 的引用是一个 RHS 引用，因为这里没有东西被赋值给 a，而是我们在查询 a 并取得它的值，这样这个值可以被传递进 console.log(..)
---
## a = 2;
- a 的引用是一个 LHS 引用，因为我们实际上不关心当前的值是什么，我们只是想找到这个变量，将它作为 = 2 赋值操作的目标
---
### 注意

- LHS 和 RHS 意味着“赋值的左/右手边”未必像字面上那样意味着“ = 赋值操作符的左/右边”
- 赋值有几种其他的发生形式，所以最好在概念上将它考虑为："赋值的目标(LHS)"和“赋值的源(RHS)"
---
### Example
```javascript
function foo(a) {
	console.log( a ); // 2
}

foo( 2 );
```

```
引擎：嘿 作用域，我有一个 foo 的 RHS 引用。听说过它吗？

作用域；啊，是的，听说过。编译器 刚在一秒钟之前声明了它。它是一个函数。给你。

引擎：太棒了，谢谢！好的，我要执行 foo 了。

引擎：嘿，作用域，我得到了一个 a 的 LHS 引用，听说过它吗？

作用域：啊，是的，听说过。编译器 刚才将它声明为 foo 的一个正式参数了。给你。

引擎：一如既往的给力，作用域。再次感谢你。现在，该把 2 赋值给 a 了。

引擎：嘿，作用域，很抱歉又一次打扰你。我需要 RHS 查询 console。听说过它吗？

作用域：没关系，引擎，这是我一天到晚的工作。是的，我得到 console 了。它是一个内建对象。给你。

引擎：完美。查找 log(..)。好的，很好，它是一个函数。

引擎：嘿，作用域。你能帮我查一下 a 的 RHS 引用吗？我想我记得它，但只是想再次确认一下。

作用域：你是对的，引擎。同一个家伙，没变。给你。

引擎：酷。传递 a 的值，也就是 2，给 log(..)
```
---
### Little Test
```javascript
function foo(a) {
	var b = a;
	return a + b;
}

var c = foo( 2 );
```
- find all LHS search
- find all RHS search

---
### 嵌套的作用域
- 作用域 是通过标识符名称查询变量的一组规则 
- 但是，通常会有多于一个的 作用域 需要考虑
- 如果在直接作用域中找不到一个变量的话，*引擎* 就会咨询下一个外层作用域，如此继续直到找到这个变量或者到达最外层作用域（也就是全局作用域）

---
### Example
```javascript
function foo(a) {
	console.log( a + b );
}

var b = 2;

foo( 2 ); // 4
```
```
引擎：“嘿，foo 的 作用域，听说过 b 吗？我得到一个它的 RHS 引用。”

作用域：“没有，从没听说过。问问别人吧。”

引擎：“嘿，foo 外面的 作用域，哦，你是全局 作用域，好吧，酷。听说过 b 吗？我得到一个它的 RHS 引用。”

作用域：“是的，当然有。给你。”


```

---
### Mistakes
```javascript
function foo(a) {
	console.log( a + b );
	b = a;
}

foo( 2 );
```
---
- RHS 查询在嵌套的 作用域 的任何地方都找不到一个值，这会导致 引擎 抛出一个 ReferenceError
- 如果 引擎 在进行一个 LHS 查询，但到达了顶层（全局 作用域）都没有找到它，而且如果程序没有运行在“Strict模式”[^note-strictmode]下，那么这个全局 作用域 将会在 全局作用域中 创建一个同名的新变量，并把它交还给 引擎
- 如果一个 RHS 查询的变量被找到了，但是你试着去做一些这个值不可能做到的事，比如将一个非函数的值作为函数运行，或者引用 null 或者 undefined 值的属性，那么 引擎 就会抛出一个不同种类的错误，称为 TypeError

---
### 总结
- 作用域是一组规则，它决定了一个变量（标识符）在哪里和如何被查找
- 这种查询也许是为了向这个变量赋值，这时变量是一个 LHS（左手边）引用
- 或是为取得它的值，这时变量是一个 RHS（右手边）引用
- 作用域 相关的赋值可以通过 = 操作符发生，也可以通过向函数参数传递（赋予）参数值发生

---

### 词法作用域

- 词法作用域是在词法分析时被定义的作用域
- 词法作用域是基于，你，在写程序时，变量和作用域的块在何处被编写决定的，因此它在词法分析器处理你的代码时（基本上）是固定不变的
---

### Example
```javascript
function foo(a) {

	var b = a * 2;

	function bar(c) {
		console.log( a, b, c );
	}

	bar(b * 3);
}

foo( 2 ); 
```

---
<img src="https://github.com/getify/You-Dont-Know-JS/raw/1ed-zh-CN/scope%20%26%20closures/fig2.png" alt="alt text" width="300px" height="180px"><br>
- 气泡1 包围着全局作用域，它里面只有一个标识符：foo
- 气泡2 包围着作用域 foo，它含有三个标识符：a，bar 和 b
- 气泡3 包围着作用域 bar，它里面只包含一个标识符：c
---
### What If
```javascript
function foo(a) {

	var b = a * 2;
	var c = 1;
	function bar(c) {
		console.log( a, b, c );
	}

	bar(b * 3);
}

foo( 2 ); 
```
---
- 如果在 bar(..) 内部和 foo(..) 内部都有一个 c，那么 console.log(..) 语句将会找到并使用 bar(..) 中的那一个，绝不会到达 foo(..) 中的那一个
- 一旦找到第一个匹配，作用域查询就停止了。相同的标识符名称可以在嵌套作用域的多个层中被指定，这称为“遮蔽（shadowing）”（内部的标识符“遮蔽”了外部的标识符）
---
### Alert!
```javascript
window.a
```
- 全局变量也自动地是全局对象（在浏览器中是 window，等等）的属性，所以可以不直接通过全局变量的词法名称，而通过将它作为全局对象的一个属性引用来间接地引用
- 这是一个访问全局变量的方法，没有它全局变量将因为被遮蔽而不可访问。然而，被遮蔽的非全局变量是无法访问的

---
### 欺骗词法作用域
- with
- eval
- 欺骗词法作用域会导致更低下的性能
---
## eval-1
```javascript
function foo(str, a) {
	eval( str ); 
	console.log( a, b );
}

var b = 2;

foo( "var b = 3;", 1 ); 
```
---
## eval-2
```javascript
function foo(str) {
   "use strict";
   eval( str );
   console.log( a ); 
}

foo( "var a = 2" );
```
---
## other ways
```javascript
setTimeout(eval(),0);
setInterval(eval(),1000);
new Function('example',eval());
```
---
## with-1

```javascript
var obj = {
	a: 1,
	b: 2,
	c: 3
};

//  重复“obj”显得更“繁冗”
obj.a = 2;
obj.b = 3;
obj.c = 4;

// “更简单”的缩写
with (obj) {
	a = 3;
	b = 4;
	c = 5;
}
```
- 看起来还不错
---
## with-2

```javascript
function foo(obj) {
	with (obj) {
		a = 2;
	}
}

var o1 = {
	a: 3
};

var o2 = {
	b: 3
};

foo( o1 );
console.log( o1.a ); 

foo( o2 );
console.log( o2.a ); 
console.log( a ); 
```
---
### Bad Points
- JavaScript 引擎 在编译阶段期行许多性能优化工作。其中的一些优化原理都归结为实质上在进行词法分析时可以静态地分析代码，并提前决定所有的变量和函数声明都在什么位置，这样在执行期间就可以少花些力气来解析标识符
---
### Bad Points

- 如果 引擎 在代码中发现一个 eval(..) 或 with，它实质上就不得不 假定 自己知道的所有的标识符的位置可能是无效的，因为它不可能在词法分析时就知道你将会向eval(..)传递什么样的代码来修改词法作用域，或者你可能会向with传递的对象有什么样的内容来创建一个新的将被查询的词法作用域
---
### 函数中的作用域

```javascript
function foo(a) {
	var b = 2;

	// 一些代码

	function bar() {
		// ...
	}

	// 更多代码

	var c = 3;
}

bar(); 

console.log( a, b, c ); 
```
---
### 隐藏于普通作用域
- least privilege principle
- 拿你所编写的代码的任意一部分，在它周围包装一个函数声明，这实质上“隐藏”了这段代码

---
### Example
```javascript
function doSomething(a) {
	b = a + doSomethingElse( a * 2 );
	console.log( b * 3 );
}
function doSomethingElse(a) {
	return a - 1;
}
var b;
doSomething( 2 ); // 15
```
---
## Better solution
```javascript
function doSomething(a) {
	function doSomethingElse(a) {
		return a - 1;
	}
	var b;
	b = a + doSomethingElse( a * 2 );
	console.log( b * 3 );
}
doSomething( 2 ); // 15
```
---
## avoid conflict
```javascript
function foo() {
	function bar(a) {
		i = 3; 
		console.log( a + i );
	}

	for (var i=0; i<10; i++) {
		bar( i * 2 ); 
	}
}

foo();
```

---
### 函数作为作用域

```javascript
var a = 2;

function foo() { // <-- 插入这个

	var a = 3;
	console.log( a ); // 3

} // <-- 和这个
foo(); // <-- 还有这个

console.log( a ); // 2
```
- foo function dirty the window scope
---
## Better way
```javascript
var a = 2;

(function foo(){ // <-- 插入这个

	var a = 3;
	console.log( a ); // 3

})(); // <-- 和这个

console.log( a ); // 2
```
- function expression
- IIFE(Immediately Invoked Function Expression)

---
### 匿名函数和命名函数
```javascript
setTimeout( function(){
	console.log("I waited 1 second!");
}, 1000 );
``` 
```javascript
setTimeout( function timeoutHandler(){ // <-- 看，我有一个名字！
	console.log( "I waited 1 second!" );
}, 1000 );
```

---
### 块作为作用域
```javascript
for (var i=0; i<10; i++) {
	console.log( i );
}
```
```javascript
var foo = true;
if (foo) {
	var bar = foo * 2;
	bar = something( bar );
	console.log( bar );
}
```
- 表面上看来，JavaScript 没有块作用域的能力
---
- with
- try/catch
```javascript
try {
	undefined(); //用非法的操作强制产生一个异常！
}
catch (err) {
	console.log( err ); // error！
}
console.log( err ); // ReferenceError: `err` not found
```
---
### Let
- let 关键字将变量声明附着在它所在的任何块（通常是一个 { .. }）的作用域中。换句话说，let 为它的变量声明隐含地劫持了任意块的作用域

```javascript
var foo = true;

if (foo) {
	let bar = foo * 2;
	bar = something( bar );
	console.log( bar );
}

console.log( bar ); 
```
---
### 隐晦
- 使用 let 将一个变量附着在一个现存的块上有些隐晦

```javascript
var foo = true;

if (foo) {
	{ // <-- 明确的块
		let bar = foo * 2;
		bar = something( bar );
		console.log( bar );
	}
}

console.log( bar ); // ReferenceError
```
---
### const
- const也创建一个块作用域变量，但是它的值是固定的（常量）。任何稍后改变它的企图都将导致错误

```javascript
var foo = true;

if (foo) {
	var a = 2;
	const b = 3; // 存在于包含它的`if`作用域中

	a = 3; // 没问题！
	// b = 4; // 错误！
}

console.log( a ); // 3
console.log( b ); // ReferenceError!
```

---
### Hoisting 提升
- 先有鸡还是先有蛋？

```javascript
a = 2;

var a;

console.log( a );
```
```javascript
console.log( a );

var a = 2;
```
---
## function hoisting
```javascript
foo(); // 不是 ReferenceError， 而是 TypeError!

var foo = function bar() {
	// ...
};
```
```javascript
foo(); // TypeError
bar(); // ReferenceError

var foo = function bar() {
	// ...
};
```
---
### Function first!
```javascript
foo(); 

var foo;

function foo() {
	console.log( 1 );
}

foo = function() {
	console.log( 2 );
};
```
---
## But 
```javascript
foo(); 
function foo() {
	console.log( 1 );
}
var foo = function() {
	console.log( 2 );
};
function foo() {
	console.log( 3 );
}
```
```javascript
foo(); // "b"
var a = true;
if (a) {
   function foo() { console.log( "a" ); }
}
else {
   function foo() { console.log( "b" ); }
}
```
---
### Conclusion
- 在同一个作用域内的重复定义是一个十分差劲儿的主意，而且经常会导致令人困惑的结果。
---
### Closure
- 什么是闭包
- 闭包就是函数能够记住并访问它的词法作用域，即使当这个函数在它的词法作用域之外执行时。

---
### Easy Example

```javascript
function foo() {
	var a = 2;

	function bar() {
		console.log( a ); // 2
	}

	bar();
}

foo();
```
- Is this a closure?
- 从纯粹的学院派角度讲，上面的代码段被认为是函数 bar() 在函数 foo() 的作用域上有一个 闭包（而且实际上，它甚至对其他的作用域也可以访问，比如这个例子中的全局作用域）

---
### expose the Closure

```javascript
function foo() {
	var a = 2;
	function bar() {
		console.log( a );
	}
	return bar;
}
var baz = foo();
baz(); //This is a closure
```
- foo() 被执行之后，一般说来我们会期望 foo() 的整个内部作用域都将消失，因为我们知道 引擎 启用了 垃圾回收器 在内存不再被使用时来回收它们。因为很显然 foo() 的内容不再被使用了，所以看起来它们很自然地应该被认为是 消失了
---
### Magic
- foo() 内部的作用域实际上 依然 “在使用”，因此将不会消失。谁在使用它？函数 bar() 本身
- 依赖于它被声明的位置，bar() 拥有一个词法作用域闭包覆盖着 foo() 的内部作用域，闭包为了能使 bar() 在以后任意的时刻可以引用这个作用域而保持它的存在
- bar() 依然拥有对那个作用域的引用，而这个引用称为闭包
---
### Usage of closure-1
```javascript
function foo() {
	var a = 2;
	function baz() {
		console.log( a ); // 2
	}
	bar( baz );
}
function bar(fn) {
	fn(); //This is a closure
}
```
---
### Usage of Closure-2
```javascript
    var fn;
    
    function foo() {
    	var a = 2;
    
    	function baz() {
    		console.log( a );
    	}
    
    	fn = baz; // 将`baz`赋值给一个全局变量
    }
    
    function bar() {
    	fn(); // This is a closure
    }
    
    foo();
    
    bar(); // 2
```
---
### reality of Closure
```javascript
function wait(message) {

	setTimeout( function timer(){
		console.log( message );
	}, 1000 );

}

wait( "Hello, closure!" );
```
---
### Loop and Closure
```javascript
for (var i=1; i<=5; i++) {
	setTimeout( function timer(){
		console.log( i );
	}, i*1000 );
}
```
---
### Try this
```javascript
for (var i=1; i<=5; i++) {
	(function(){
		setTimeout( function timer(){
			console.log( i );
		}, i*1000 );
	})();
}
```
---
### Now
```javascript
for (var i=1; i<=5; i++) {
	(function(){
		var j = i; //<= insert a j to represent i
		setTimeout( function timer(){
			console.log( j );
		}, j*1000 );
	})();
}
```
---
### using block - Let
```javascript
for (var i=1; i<=5; i++) {
	let j = i; // 利用let限制了作用域
	setTimeout( function timer(){
		console.log( j );
	}, j*1000 );
}
```
---
### Better way
```javascript
for (let i=1; i<=5; i++) {
	setTimeout( function timer(){
		console.log( i );
	}, i*1000 );
}
```
---
### Module and Closure
```javascript
function foo() {
	var something = "cool";
	var another = [1, 2, 3];

	function doSomething() {
		console.log( something );
	}

	function doAnother() {
		console.log( another.join( " ! " ) );
	}
}
```
---
### expose the closure
```javascript
function CoolModule() {
	var something = "cool";
	var another = [1, 2, 3];

	function doSomething() {
		console.log( something );
	}

	function doAnother() {
		console.log( another.join( " ! " ) );
	}

	return {
		doSomething: doSomething,
		doAnother: doAnother
	};
}

var foo = CoolModule();

foo.doSomething(); // cool
foo.doAnother(); // 1 ! 2 ! 3
```
---
### singleton
```javascript
var foo = (function CoolModule() {
	var something = "cool";
	var another = [1, 2, 3];

	function doSomething() {
		console.log( something );
	}

	function doAnother() {
		console.log( another.join( " ! " ) );
	}

	return {
		doSomething: doSomething,
		doAnother: doAnother
	};
})();

foo.doSomething(); // cool
foo.doAnother(); // 1 ! 2 ! 3
```
---
### modules are function basically
```javascript
function CoolModule(id) {
	function identify() {
		console.log( id );
	}

	return {
		identify: identify
	};
}

var foo1 = CoolModule( "foo 1" );
var foo2 = CoolModule( "foo 2" );

foo1.identify(); // "foo 1"
foo2.identify(); // "foo 2"
```
---
### Modern Modules
```javascript
var MyModules = (function Manager() {
	var modules = {};
	function define(name, deps, impl) {
		for (var i=0; i<deps.length; i++) {
			deps[i] = modules[deps[i]];
		}
		modules[name] = impl.apply( impl, deps );
	}
	function get(name) {
		return modules[name];
	}
	return {
		define: define,
		get: get
	};
})();
```
- modules[name] = impl.apply(impl, deps) 这为一个模块调用了它的定义的包装函数（传入所有依赖），并将返回值，也就是模块的API，存储到一个用名称追踪的内部模块列表中
---
### Modern Modules
```javascript
MyModules.define( "bar", [], function(){
	function hello(who) {
		return "Let me introduce: " + who;
	}

	return {
		hello: hello
	};
} );

MyModules.define( "foo", ["bar"], function(bar){
	var hungry = "hippo";

	function awesome() {
		console.log( bar.hello( hungry ).toUpperCase() );
	}

	return {
		awesome: awesome
	};
} );

var bar = MyModules.get( "bar" );
var foo = MyModules.get( "foo" );

console.log(
	bar.hello( "hippo" )
); // Let me introduce: hippo

foo.awesome(); // LET ME INTRODUCE: HIPPO
```
---
### Modules in ES6
- ES6 为模块的概念增加了头等的语法支持
- ES6 将一个文件视为一个独立的模块
- 每个模块可以导入其他的模块或者特定的API成员，也可以导出它们自己的公有API成员
- ES6 模块API是静态的（这些API不会在运行时改变）。因为编译器知道它，它可以在（文件加载和）编译期间检查一个指向被导入模块的成员的引用是否 实际存在。如果API引用不存在，编译器就会在编译时抛出一个“早期”错误，而不是等待传统的动态运行时解决方案
---
### Example-1
```javascript
//bar.js
function hello(who) {
	return "Let me introduce: " + who;
}
export hello;
```
```javascript
//foo.js
import hello from "bar";
var hungry = "hippo";
function awesome() {
	console.log(
		hello( hungry ).toUpperCase()
	);
}
export awesome;
```
---
### Example-2
```javascript
// 导入`foo`和`bar`整个模块
module foo from "foo";
module bar from "bar";

console.log(
	bar.hello( "rhino" )
); // Let me introduce: rhino

foo.awesome(); // LET ME INTRODUCE: HIPPO
```

- 在 模块文件 内部的内容被视为像是包围在一个作用域闭包中，就像早先看到的使用函数闭包的模块那样
---
### Now Test
```javascript
function foo(x) {
  var tmp = 3;
  function bar(y) {
    alert(x + y + (++tmp));
  }
  bar(10);
}
foo(2)
```
---
```javascript
function foo(x) {
  var tmp = 3;
  return function (y) {
    alert(x + y + (++tmp));
  }
}
var bar = foo(2); // bar 现在是一个闭包
bar(10);
```