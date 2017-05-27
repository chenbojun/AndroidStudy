1、JS没有块级作用域：

* 全局作用域
* 函数作用域（局部作用域）：函数内部、with内部、for内部等（catch内部的Exception仅catch语句块可访问）

2、JS的函数定义特殊性：

 * 参数不是必须的
 * 不存在重载函数
 * 隐藏的内部变量arguments（类似数组的方式访问）
 
3、JS的垃圾回收机制：

自动、定期or预定、根据不同浏览器的“标识无用变量策略”而不同

 * 标记清除法
 * 饮用计数法：存在循环引用问题！！！

4、内存最佳实践

 * 自动解除引用（防止循环引用+有效回收内存）

5、引用类型

 * 定义方式 

 		var person = new Object();
 		person.name = "jack";
 		
 		var person = {
 			name: "jack"
 		}
 	
 * 支持中括号访问（建议用.方法访问，除非变量名包含空格必须使用中括号）
 
  		person["name"] or person.name 
  	
 * 数组：
 
 	* length具有读写性质（通过写增元素）
 	* Array.join(分隔符号)
 	* 支持push／pop方法（栈）
 	* 支持shift／unshift方法（模拟队列）
 	
          // 取得数组前端的第一项（同时第一项移出队列）
 			var item = colors.shift();
 			// 在数组前端添加2元素
 			colors.unshift("red", "blue");
 		
 	* 排序：sort，支持传入比较函数自定义排序规则
 	* concat： 获取拼接后的副本，把数组本身+参数（不影响原来数组）
 	* slice：获取子数组的副本（不影响原来数组）

 			colors.slice(0) or colors.slice(1,3)
 	* splice：删除、插入、替换（影响数组本身）

 			splice(int start, int removeCount, ```插入项)
 			
 	* 位置方法：indexOf（int start， var item） ／ lastIndexOf（int start， var item）
 	
 	* 迭代方法：传入参数为function（item，index，array）
 	   
 	   item只是对应位置的元素副本，对其操作不会影响数组本身（可以通过array[index]操作数组元素）
 		* every
 		* some
 		* filter
 		* map
 		* foreach（相当于对数组进行for循环）
 	
 	* reduce／reduceRight方法
 	       
 	       // 返回最终的prev
 			arrays.reduce(function(prev, cur, index, array) {
 			});
 	
 * Date
 
 	* Date(毫秒数)/Date(year, month, day, hour, minute, second)/
 	* Date.parse()/Date.UTC(year, month, day, hour, minute, second)
 	* Date.now()
 	* 可直接比较大小
 
 * **RegExp**(Todo 有待学习)
 
 * Function
 
 函数本质上是对象，函数名就是指针
 
 	* 函数声明 vs 函数表达式：解析器提前读取函数声明！函数表达式仅运行代码再读取

             	// 函数声明（合法）
	 			alert(sum(10,10))
	 			function sum(num1, num2) {
	 				return num1 + num2;
	 			}
	 			// 函数表达式（错误）
	 			alert(sum(10,10)）
	 			function sum(num1, num2) {
	 				return num1 + num2;
	 			}
	* 函数可以作为值进行传递：（厉害，例如：动态指定数组排序的依据字段）
	* 函数内部属性：
	 	* arguments：参数（可通过arguments.callee,相当于当前函数本身，进行递归调用）
	 	* this：    指向函数调用的执行环境
	 	* caller：  函数本身的属性，保存调用当前函数的函数的引用
	* 函数属性和方法：
	 	* length: 命名参数的个数
	 	* prototype: 不可枚举，包含所有实例方法（访问时通过实例对象调用实例方法）
	 	* apply方法／call方法：厉害啦！用于指定函数赖以运行的作用域，实现对象和方法的解耦

	 			apply(作用域, arguments)
	 			call(作用域，param1,param2,···)
	 			
* 基本包装类型
	* 尽量不使用Boolean包装类型
	* Number：提供格式化函数
	* String：Todo 好好学！！！（需要某些功能则浏览书本）

* 单体内置对象
	* Global对象
	* Math对象 

6、面向对象的程序设计

* 理解对象
	* 数据属性
		* Obejct.defineProperty(对象、属性名、特性组)
	
				var person = {};
				Object.defineProperty(person, "name", {
					Configurable:true //是否可重新定义属性，如delete
					Enumerable:true //是否可enum遍历属性
					writable:true //是否可写
					value:"xiaoming" //属性值
				});
	* 访问器属性
		* Obejct.defineProperty(对象、属性名、特性组)
	
				var person = {};
				Object.defineProperty(person, "name", {
					Configurable:true //是否可重新定义属性，如delete
					Enumerable:true //是否可enum遍历属性
					get:function{} //定义get函数
					set:function{} //定义set函数
				});
	* 定义多个属性
	 			
	 			varbook = {};
				Object.defineProperty(book, {
				   // 数据属性
					_year: {
						value:2004
					},
					// 数据属性
					edition: {
						value:1
					},
					// 访问器属性
					year: {
						get: function() {
							return this._year;
						},
						set: function(newValue) {
							this._year = newValue;
						}
					}
				});
				
	* 读取属性的特性

			var descriptor = Object.getOwnPropertyDescriptor(book, "_year");
			alert(descripter.value);
			alert(descripter.configurable)
			
* 创建对象：
	* 	工厂模式
	*  构造函数模式！！！必须参考书本栗子～
		* 构造函数的弊端？？？每个示例的示例函数都是一个对象（解决方案，把实例函数在构造函数外定义，附加的浪费问题-》原型模式）	  
	* 原型模式
	* 