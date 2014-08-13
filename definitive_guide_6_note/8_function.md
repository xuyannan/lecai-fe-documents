##函数基本概念
* 函数是一段javascript代码，它只定义一次，但可能被执行或调用任意次。 函数即对象。


> 形参，实参  	

	function abc(a, b) { 
	}    
	abc(1, 2);


##8.1 函数定义

* 一条函数声明语句实际上声明了一个变量，并把一个函数对象赋值给它。相对而言，定义函数表达式时并没有声明一个变量。

###表达式方式    
* 以表达式方式定义的函数名是可选的。

* 表达式方式的作用域简单来说就是从定义了以后才有，所以这有个定义顺序的问题。

* 以表达式方式定义函数，函数的名称将成为函数内部的一个局部变量。    

		var fn = function abc(){ console.log(abc.toString()) };

###声明方式
* 以声明方式定义的函数作用域被提前到外部脚本或外部函数作用域的顶部

* 函数声明语句并非真正的语句，ECMAScript规范只是允许它们作为顶级语句。它们可以出现在全局代码里，或者内嵌在其他函数中，但它们不能出现在循环、条件判断，或者try/cache/finally以及with语句中。


##8.2 函数调用
* 4种方式来调用函数：

		作为函数  
			abc();  
		作为方法  
			obj.abc();  
		作为构造函数      
			new abc();  
		通过它们的call()和apply()方法间接调用  
			abc.call(this); 
			abc.apply(this); 

* 在函数体中存在一个形参的引用（arguments），指向当前传入的实参列表，通过它可以获得参数的值。	

* 定义的时候并不会执行，只有调用该函数时，才会执行。

* this是一个关键字，不是变量，也不是属性名。JavaScript的语法不允许给this赋值。关键字this没有作用域的限制，嵌套的函数不会从调用它的函数中继承this。

* 对于声明方式定义的函数，ECMAScript3和非严格的ECMAScript5对函数调用的规定,调用上下文（this的值）是全局对象。然而，在严格模式下，调用上下文则是undefined。    

		function a() { console.log('--',this); } a();
	
		function a() { 'use strict'; function b() { console.log('function b return', this); } b(); return 'function a return'; } a();
	
		var strict = (function() {return !this;});

###方法调用
* 任何函数只要作为方法调用实际上都会传入一个隐式的实参--这个实参是一个对象，方法调用的母体就是这个对象。

###构造函数调用
* 没有形参的构造函数可以省略圆括号。

* 构造函数调用创建一个新的空对象，这个对象继承自构造函数的prototype属性。构造函数试图初始化这个新创建的对象，并将这个对象用做其调用上下文，因此构造函数可以使用this关键字来引用这个新创建的对象。注意，尽管构造函数看起来像一个方法调用，它依然会使用这个新对象作为调用上下文。也就是说在表达式new o.m()中，调用上下文并不是o

		var o = { m: function() { console.log('----',this) }}; new o.m(); o.m();


* 构造函数通常不使用return关键字，它们通常初新对象，当构造函数的函数体执行完毕时，它会显式返回。在这种情况下，构造函数调用表达式的计算结果就是这个新对象的值。然而如果构造函数显式地使用return语句返回一个对象，那么调用表达式的值就是这个对象。如果构造函数使用return语句但没有指定返回值，或者返回一个原始值，那么这里将忽略返回值，同时使用这个新对象作为调用结果。

		function A(){ this.b = 'b'; return 'asdf'; } var a = new A(); a.b;
		
		function A(){ this.b = 'b'; return {}; } var a = new A(); a.b;

###间接调用
	call()
	Object.prototype.toString.call(this);
	apply()
	Math.max.apply(Math, [2,1,3,5,4]);


##8.3 函数的实参与形参
	
* arguments 实参对象，不是数组，是个伪数组。
* arguments并不是一个关键字，但在调用每个函数时都会自动声明它。
	
* 在非严格模式下，当一个函数包含若干形参，实参对象的数组元素是函数形参所对应实参的别名，实参对象中以数字索引，并且形参名称可以认为是相同变量的不同命名。

		function f(x) {
			console.log(x);
			arguemtns[0] = null;
			console.log(x);
		}

* 在ECMAScript5中移除了实参对象的这个特殊特性。 在严格模式下还有一点（和非严格模式下相比的）不同，在非严格模式中，函数里的arguments仅仅是一个标识符，在严格模式中，它变成了一个保留字。严格模式中的函数无法使用arguments作为形参名或局部变量名，也不能给arguments赋值。

		(function(){
			function abc(a, b) {
				'use strict';
				console.log(a);
				arguments[0] = 3;
				console.log( arguments[0] );
			}    
			abc(1, 2);
		})();

* callee 和 caller 在ECMAScript5严格模式中，对这两个属性的读写操作都会产生一个类型错误。

##8.4 作为值的函数
##8.5 作为命名空间的函数
* 如果一个对象拥有一个不可枚举的同名属性，刚for/in循环不会枚举对象的可枚举属性，也就是说，将不会正确地处理诸如toString的属性，除非我们显式检测它。
	
* 在ie6下有问题不会输出
		for (var p in {toString: null}) {
			console.log('good');
		}

##8.6 闭包
* 变量的作用域是在函数**定义时**决定的，而不是函数调用时决定的。

* 容易出错的地方

		function constfuncs(){
			var funcs = [];
			for(var i = 0; i < 10; i++){
				funcs[i] =  function() { return i; };
			}
			return funcs;
		}
		var funcs = constfuncs();
		funcs[5]();
    
##8.7 函数属性、方法和构造函数
* function的length属性返回形参的个数

		function check(args){
			var actual = args.length;
		    var expected = args.callee.length;
		    if ( actual !== expected) {
		    	throw Error('Expected '+ expected + ' args; got '+ actual);
		    }
		}
		function f(x, y, z){
			check(arguments);
		    return x + y + z;
		}

* prototype属性指向一个对象的引用，这个对象称做“原型对象”。当将函数用做构造函数的时候，新创建的对象会从原型对象上继承属性。
	
* 传入apply()的参数数组可以是类数组对象也可以是真实数组。

* bind()是在ECMAScript5中新增的方法，主要作用是将函数绑定至某个对象。当在函数f()上调用bind()方法并传入一个对象o作为参数，这个方法将返回一个新的函数。（以函数调用的方式）调用新的函数将会把原始的函数f()当做o的方法来调用。传入新函数的任何实参都将传入原始函数。
		function f(y) { return this.x + y; }
		var o = { x : 1 };
		var g = f.bind(o);
		g(2);

* ECMAScript5中的bind()方法不仅仅是将函数绑定至一个对象，它还附带一些其他应用：除了第一个实参之外，传入bind()的实参也会绑定至this。

##8.8 函数式编程	

##引申
DOM对象与javascript对象之间引用

		function bindEvent() 
		{ 
		    var obj=document.createElement("XXX"); 
		    obj.onclick = function(){ 
		        //Even if it's a empty function 
		    } 
		}

正确是这样

		function bindEvent() 
		{ 
		    var obj=document.createElement("XXX"); 
		    obj.onclick = onclickHandler; 
		} 
		function onclickHandler(){ 
		    //do something 
		}
或这样	

		function bindEvent() 
		{ 
		    var obj=document.createElement("XXX"); 
		    obj.onclick=function(){ 
		        //Even if it's a empty function 
		    } 
		    obj=null; 
		}

作用域链  
http://www.cnblogs.com/lhb25/archive/2011/09/06/javascript-scope-chain.html
