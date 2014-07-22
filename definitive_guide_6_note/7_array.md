数组是 值的有序集合，每个值叫元素，元素的位置用数字表示称为索引  
数组的范围 0-2^32-2  42.9亿  
数组是动态的（自动增长或缩小）数组是稀疏的（元素的索引不一定连续，之间可以有空缺） 

## 7.1 创建数组
+  数组直接量[]
+  new Array(10)

## 7.2 读和写
数组索引和对象属性的区别
>数组的索引是*非负整数 0-2^32-1*，数组需要*维护length属性*， 所有数组都是对象  
>对象的索引是字符串 
  
数组索引的隐式转换  
>   如果索引是 负数或者非整数，会转换成字符串，且当成对象是使用  
>   如果索引是 非负整数的字符串或者1.00这样的浮点数时，会转成非负整数，作为数组使用
  
## 7.3 稀疏数组
>不连续索引的数组。length的值大于数组总元素的个数。

	1 s = Array(5),长度为5，但是总的元素为0  
	2 s[1000] = 0;
	3 delete s[2]

>稀疏数组查找速度慢，时间跟对象查找属性时间类似

## 7.4 数组长度
>每个数组都有length属性，有两个特性  


1. length属性值大于数组元素个数
2. length属性值小于当前数组元素个数时，索引值大的元素会被删除

		var arr = [1,2,3,4,5];
		arr.length = 3;   //[1,2,3]
		arr.length = -1; //RangeError 

## 7.5 添加和删除
### 添加
	var arr = [];
	1. arr[0] = 1;
	2. arr.push(1);
	3. arr.unshift(1);
###删除
>delete删除元素，不会修改length属性

	var arr = [1,2];
	1. delete arr[0]; 1 in arr;  //false 
	2. arr.pop();
	3. arr.shift();
>用splice来添加和删除

## 7.6 遍历

+ for (var i = 0; i < arr.length; i++)
	>可以通过 for(var i=0,len=arr.length; i<len; i++)做优化  
	>针对常规的数组几乎没效果。对于每次计算.length值的如dom集合等伪数组影响极大
		
		var arr = new Array(10000000);
		var d1 = new Date;
		for (var i = 0,len = arr.length;i<len;i++){}
		console.log(new Date - d1);
		var d2 = new Date;
		for (var i=0; i<arr.length; i++){}
		console.log(new Date - d2)

+ for (var i in arr)
	> 不建议使用，会把自定义的一些可枚举的属性显示出来，通过下面的方式去掉  
	> 先判断是否是自身的属性arr.hasOwnProperty(i)，再判断是否是非负整数  
	> for in 遍历出的属性的顺序不一定是按照数值大小排序的，可能是按照创建时的顺序，//试了一下按照先数字属性升序，后字符串属性
+ arr.forEach()

## 7.7 多维数组

## 7.8 数组方法
###ecma3中的方法
[1,2,3].join() 默认用逗号做分割  
  
[1,2,3].reverse -> [3,2,1]  

[1,2,,11,12].sort() -> [1,11,12,2] 默认按字母顺序，undefined放在最后  

[3,2,1].concat([2,[1,3]]，5) -> [3,2,1,2,[1,3],5] 只能连接一层数组，不能递归  

var arr = [1,2,3,4,5]

	arr.slice(0,3) -> [1,2,3]  
	arr.slice(3) -> [4,5]  
	arr.slice(1,-1) -> [2,3,4]  
	arr.slice(-3,-2) -> [3]

var s=[1,2,3,4,5,6,7,8];  返回被删除的元素，自身改变为修改后的值。第一二个参数表示删除，后面的参数表示新加的元素  

    s.splice(4)
    s.splice(1,2)
    s.splice(0,1,[5,5],6)

push(1,2,3) 可以多个参数 pop() 不加参数

unshift(1,2,3) 可以多个参数 shift() 不加参数

[1,2,[3,4],{a:1}].toString() -> '1,2,3,4,[object Object]' 将每个元素转化为字符串，并用逗号分隔  

[1,2,[3,4],{a:1}].toLocaleString() 返回本地化的数组分隔方式，试了下同toString没有区别
___
  
###ecma5的方法中 
>大都是这个情况  

+ 第一个参数是函数  
+ 每个数组的元素都会调用这个函数，稀疏数组中不存在的元素忽略不会调用
+ 函数的参数分别是 元素 索引 数组
+ 这些函数不会修改原来的数组
+ 如果除了函数，还有第二个参数，那就把这个函数作为第二个参数的方法，this是第二个参数 
    
  
[1,2,3].forEach(function(ele,index,arr){}); 没有返回值  

    会从头遍历数组，如果想提前终止只能通过try catch
    try{
      [1,2,3,4,5].forEach(function(ele,index,arr){
        console.log('1');
        throw new Error('...')
      }) 
    }catch(e){
      console.log(e);
    }
  
[1,2,3].map(function(ele,index,arr){});  
>同forEach类似，自身不改变，但多了返回值。如果是稀疏数组，同样返回稀疏数组！
  
[1,2,3,,undefined,null].filter(function(ele,index,arr){return });
>用来判定如果返回值是true，就做为新的成员。另外返回值总是稠密的！

[1,2,3,,undefined,null].every(function(ele,index,arr){return });
>用来判定数组中的所有元素都满足条件,返回布尔值。 

[1,2,3,,undefined,null].some(function(ele,index,arr){return });
>用来判定数组中的只要1个元素都满足条件,返回布尔值。  
every和some，在满足一个条件的情况下就会终止

[1,2,3,1,1].reduce(function(a,b){return a+b},0);
>做左至右做依次的化简操作，函数作为化简的条件，第二个参数作为初始值可不填。返回最终化简的值。  
原理: 0作为a，1作为b。相加得1作为a，2作为b。相加得3作为a......  
有第二个参数的话，作为函数的第一个参数，没有的话数组的第一个元素作为函数的第一个参数。  
如果函数没有参数会报错，函数只有1个参数，不执行函数，直接返回这个参数。  
reduceRight 是从右边开始做操作

[1,,2].indexOf(2) ->  2
> 返回元素在数组中的位置 如果不存在返回-1,空值也算一个位置  
> lastIndexOf 反向搜索
> 可以带第二个参数，表示从哪个位置开始搜索。  
> 不带的话，表示从0开始搜索，lastIndexOf表示从最后一个-1开始搜索。

## 7.10 数组类型
+ Array.isArray([]) -> true
+ [] instanceOf Array -> true //不是很靠谱的方法，在有iframe的情况下，不同窗体间的构造函数Array不同 [一个例子](http://yueyemaitian.iteye.com/blog/858108)
+ Object.prototype.toString.call([]).slice(8,-1) -> Array

## 7.11 伪数组
> 伪数组就是拥有*非负整数属性*和*length属性*的对象  
> Arguments对象，一些dom方法返回的对象(getElementsByTagName) 等是伪数组对象  
> 可以通过call来调用数组的大部分方法。部分如concat方法不能用  
> ecma5中新定义的一些方法可以直接通过静态属性使用 Array.slice({0:0,1:1,2:2,length:3},1)

数组的特性

+ 自动更新length值
+ length值小时，截断数组
+ 从Array.prototype继承属性
+ 对象特性class为Array


## 7.12 作为数组的字符串
>ie8及以后的版本支持，字符串的行为类似于__只读数组!__。  
>如 1 Array.prototype.join.call('hello','-'); 2 'hello'[1]  
>一些修改数组的操作会报错 如 Array.prototype.sort.call('123');