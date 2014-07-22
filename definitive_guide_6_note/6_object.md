对象 是属性的无序集合，属性名是字符串，每个属性都是一个key/value对。对象可以看成是从字符串到值的映射  
这种数据结构又称散列hash，散列表hashtable，字典dictionary，关联数组associative array  

对象可以从 原型 继承属性，方法通常都是继承的，  

除了五大原始类型外，js中的值都是对象  

对象的常见用法 create set query delete test enumerate  

对象分成3类 1 native object  2 host object 3 user-defined object  

### 6.1 create
+ 对象直接量 
+ Object.create
+ new
### 6.2 set query
属性两类

+ own property
+ inherited property  

构造函数

+ 内置构造函数
+ 自定义构造函数

> 对对象属性的操作只影响本身，不影响原型链中对象的属性，但可以读取到原型链中属性的值  
set和get可以继承，且覆盖相同的属性，使用的是继承的set和get，且这里的this是自身，不是原型链中的  

以下情况属性赋值会失败

+ writable属性是只读的，（不过可以通过设置configrable来修改writable）
+ 如果继承属性中有只读的，不能覆盖
+ 继承了set，get。但自身是不可扩展的

#
	var s={
	  x:1,
	  set p(x) {
		this.x = x;
	  },
	  get p() {
		return this.x;
	  }
	};
	var o=Object.create(s);
	Object.preventExtensions(o);
	o.x=2;
	o.x

### 6.3 delete
删除只是断开对象与属性的联系

	var a = {p:{x:1}};
	var b = a.p;
	delete a.p;
	b.x

### 6.4 test
+ in 自有属性和继承属性
+ hasOwnProperty  自有属性
+ propertyIsEnumerable 自有属性且可枚举

### 6.5 enumerate
+ for in 自身和继承中可枚举的属性
+ Object.keys() 返回一个可枚举的自有属性的数组
+ Object.getOwnPropertyNames() 返回自有的所有属性的数组

### 6.6 存取器属性accessor property 由getter setter定义的属性
数据属性 data property

	var p = {
	  a:1,
	  get b() {
	    return this.m;
	  },
	  set b(x) {
	    this.m = x+1
	  }
	};
	p.b=2
	p.b

###6.7 属性的特性（property attribute）
+ writable 
+ enumerable 
+ configurabe(删除,不能修改枚举和可配置,不能将writable从false改成true)

>ecma3中，自定义的属性都是满足上面条件的，同时不可修改属性的特性。ecma5中可自己修改


accessor property 寄存器属性的特性

1.  get
2.  set  
3.  enumerable 
4.  configurable

data property 普通的数据属性的特性  

1. value  
2. writable 
3. enumerable 
4. configurable


>对以上属性特性的操作通过property descriptor这个对象获取特性  
>只能针对自身的属性 Object.getOwnPropertyDescriptor({x:1},'x')设置特性  
>不能修改继承的属性的特性，可以对已有的或者新加的做设置  

	var p={};
	Object.defineProperty(p,'x',
	  {value:1,writable:true,enumerable:false,configable:true}
	)
	
	Object.defineProperties({},{....})

属性特性间遵循下列原则

+ 对象不可扩展，则可以编辑自有属性，不能添加新的属性
+ 属性是不可配置，则不能修改配置性和枚举性
+ 存取器属性不可配置，则不能修改get和set，不能转成数据属性
+ 数据属性不可配置，则不能转成存取器属性，可写性只能从true转为false
+ 数据属性不可配置且不可写，则不能改值
+ 数据属性可配置但不可写 ，值可以修改

### 6.8 每个对象都有三个特性。
+ 1 prototype 
+ 2 class 
+ 3 extensible


####### 1 prototype
查询对象o的原型

+ Object.getPrototypeOf(o)
+ o.constructor.prototype

>Object.create创建的对象,constructor.prototype是Object.prototype 所以不能完全通过上面constructor方式来  

检测一个对象是否处于另一个对象的原型链中  

+ o.isPrototypeOf(p) 
+ ({}) instanceof Object

####### 2 class
>[object class]  
Object.prototype.toString.call(o).slice(8,-1)

####### 3 可扩展性
>表示是否可以给对象添加新的属性。  
>所有内置对象和自定义对象都是可扩展的
***  
>检查是否可扩展 Object.isExtensible(o)  
>转成不可扩展  Object.preventExtensions(o) = 不能添加  
>如果是不可扩展的就不能转成可扩展的了，不可扩展值只针对于自身不对于原型链  
***
>Object.seal(o) = Object.preventExtensions() + configurable:false（不能删除，不能修改枚举和可配置，不能将writable从false改成true）
>
	var s= {a:1};  
	Object.seal(s);  
	Object.getOwnPropertyDescriptor(s,'a')  
>Object.isSealed(o)
***  
>Object.freeze(o) = Object.seal() + writable:false = 不能添加删除 + 不可修改  
Object.isFrozen(o)
***
	var s = Object.seal(
	  Object.create(
		Object.freeze({x:1}),
		{y:{value:2,writable:true}}
	  )
	);
	s
	s.x
	s.y
	s.x=3
	s.y=3
	s.m=2
	delete s.y
	delete s.x,


toString 有各自的实现，Object的toString是返回 object class  
valueOf 转换为原始值而非字符串