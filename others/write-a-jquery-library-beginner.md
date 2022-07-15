# 从零一步步创建一套替代jQuery的方案


## 目录

 - [第一步(测试构造函数)](#第一步测试构造函数)
 - [第二步(测试dom操作))](#第二步测试dom操作)
 - [第三步(结果中直接返回可用的数组节点)](#第三步结果中直接返回可用的数组节点)
 - [第四步(自动化应用apply()方法到每个元素节点上)](#第四步自动化应用apply方法到每个元素节点上)
 - [第五步1(最后改进)](#第五步1最后改进)
 - [第五步2(不使用new关键字的写法测试)](#第五步2不使用new关键字的写法测试)
 - [第五步3(不使用new关键字的写法测试)](#第五步3不使用new关键字的写法测试)
 - [第五步4(不使用new关键字的写法测试)](#第五步4不使用new关键字的写法测试)
 - [第五步5(不使用new关键字的写法测试,模拟构造函数,将prototype的名称和方法应用到每个对象上)](#第五步5不使用new关键字的写法测试模拟构造函数将prototype的名称和方法应用到每个对象上)
 

### 第一步(测试构造函数)

```js
const $ = (function () {

	'use strict';
	
	//this.elems = [];  //如果这里写一个，就报错： Uncaught TypeError: Cannot set properties of undefined (setting 'elems')

	/**
	 * Create the constructor
	 * @param {String} selector The selector to use
	 */
	const Constructor = function (selector) {
		if (selector === 'document') {
			this.elems = [document];
		} else if (selector === 'window') {
			this.elems = [window];
		} else {
			this.elems = document.querySelectorAll(selector);
		}
	};

	/**
	 * Instantiate a new constructor
	 */
	const instantiate = function (selector) {
		return new Constructor(selector);
	};

	/**
	 * Return the constructor instantiation
	 */
	return instantiate;

})();

console.log( $('#demo') ); //Constructor {elems: NodeList(1)}
console.log( $('#demo').elems ); //NodeList [div#demo]
console.log( [].slice.call( $('#demo').elems ) ); //[div#demo] (数组类型)


//注意：如果写成 return instantiate.elems, 将报错  Uncaught TypeError: $ is not a function
```


### 第二步(测试dom操作)


```js
const __ = (function () {

	'use strict';

	/**
	 * Create the constructor
	 * @param {String} selector The selector to use
	 */
	const Constructor = function (selector) {
		if (selector === 'document') {
			this.elems = [document];
		} else if (selector === 'window') {
			this.elems = [window];
		} else {
			this.elems = document.querySelectorAll(selector);
		}
	};

	/**
	 * Instantiate a new constructor
	 */
	const $ = function (selector) {
		return new Constructor(selector);
	};
	

	/**
	 * Return a prototype that extending by adding new methods
	 */
	$.fn = Constructor.prototype;

	
	/**
	 * Other methods
	 */
	 Constructor.prototype.defaultPlugin = function( str ) {
		 const el = [].slice.call( this.elems );
		 el.forEach( function(element, index) {
			element.innerHTML = str;
		 });
	 };


	return $;

})();

console.log( __('#demo') ); //Constructor {elems: NodeList(1)}
console.log( __('#demo').elems ); //NodeList [div#demo]
console.log( [].slice.call( __('#demo').elems ) ); //[div#demo] (数组类型)

__('.uix-menu__container').defaultPlugin( 'new string' );  //生效


//自定义插件
__.fn.mytPlugin = function() {
	const el = [].slice.call( this.elems );
	el.forEach( function(element, index) {
		element.style.background = '#f60';
	}); 
}
__('.uix-menu__container').mytPlugin();    //生效
```

### 第三步(结果中直接返回可用的数组节点)


```js
const __ = (function () {

	'use strict';

	/**
	 * Create the constructor
	 * @param {String} selector The selector to use
	 */
	const Constructor = function (selector) {
		if (selector === 'document') {
			this.elems = [document];
		} else if (selector === 'window') {
			this.elems = [window];
		} else {
			this.elems = document.querySelectorAll(selector);
		}

		//Return available nodes as an array
		//结果中直接返回可用的数组节点
		const el = [].slice.call( this.elems );
		const result = [];
		el.forEach( function(element, index) {
			result.push( element );
		});

		return result;

	};

	/**
	 * Instantiate a new constructor
	 */
	const $ = function (selector) {
		return new Constructor(selector);
	};

	/**
	 * Return a prototype that extending by adding new methods
	 */
	$.fn = Constructor.prototype;

	

	return $;

})();


console.log( __('.app') );  //[li.app.app1.app2, li.app, li#kkkkk.app.app3, li.app.app4, li.app.app4]
```


### 第四步(自动化应用apply()方法到每个元素节点上)

**原理1：**

```js
const __ = (function () {

	'use strict';

	/**
	 * Create the constructor
	 * @param {String} selector The selector to use
	 */
	const Constructor = function (selector) {
		if (selector === 'document') {
			this.elems = [document];
		} else if (selector === 'window') {
			this.elems = [window];
		} else {
			this.elems = document.querySelectorAll(selector);
		}

		//Return available nodes as an array
		//结果中直接返回可用的数组节点
		const el = [].slice.call( this.elems );
		const result = [];
		el.forEach( function(element, index) {
			const callbackVal = Constructor.prototype.defaultPlugin.apply(element, arguments);
			result.push( callbackVal );
		});


		//
		return result;

	};

	/**
	 * Instantiate a new constructor
	 */
	const $ = function (selector) {
		return new Constructor(selector);
	};

	/**
	 * Return a prototype that extending by adding new methods
	 */
	$.fn = Constructor.prototype;

	
	/**
	 * Other methods
	 */
	 Constructor.prototype.defaultPlugin = function() {
		this.style.background = '#f60';
	 };


	return $;

})();


__('.app');  //生效，将选中的元素背景色都改变了

```



**原理2：**


```js
const __ = (function () {

	'use strict';

	/**
	 * Create the constructor
	 * @param {String} selector The selector to use
	 */
	const Constructor = function (selector) {
		if (selector === 'document') {
			this.elems = [document];
		} else if (selector === 'window') {
			this.elems = [window];
		} else {
			this.elems = document.querySelectorAll(selector);
		}

	
	};

	/**
	 * Instantiate a new constructor
	 */
	const $ = function (selector) {
		return new Constructor(selector);
	};

	/**
	 * Return a prototype that extending by adding new methods
	 */
	$.fn = Constructor.prototype;


	/**
	 * apply中arguments的传参原理解析
	 */
	Constructor.prototype.defaultPlugin = function( color1, color2, fn ) {

		const el = [].slice.call( this.elems );
		el.forEach( function(element) {
			element.style.background = color1;

			/*
			arguments将 forEach()的所有参数都自动全部带入传递，
			不论forEach( function(element) {}) 的function写的是多少参数，本身forEach有3个参数就传递3个

			---
			arr.forEach(callback(currentValue [, index [, array]])[, thisArg])
			为数组中每个元素执行的函数，该函数接收一至三个参数：
			currentValue
			数组中正在处理的当前元素。
			index 可选
			数组中正在处理的当前元素的索引。
			array 可选
			*/
			console.log(arguments); // Arguments(3) [currentValue,index,array]
			console.log([].slice.call(arguments));  // [currentValue,index,array] (变成纯数组)

			fn.apply(element, [].slice.call(arguments) );
			setTimeout( () => {
				element.style.background = color2;
			}, 1000);	
		});

		return this;

	};


	return $;

})();


__('.app').defaultPlugin( '#f60', '#333', function(a,b,c,d) {
	console.log("a: ", a, " b: ", b, " c: ", c, " d: ", d);  
	// (上面的arguments的值，没有则返回undefined)
	//输出：
	//a: currentValue
	//b: index
	//c: array
	//d: undefined 
	
}); 
```

### 第五步1(最后改进)


```js
//【正确的写法】
//【注意：如果把Constructor.prototype写成 $$.prototype 就会报错： Uncaught TypeError: __(...).someMethod is not a function】

const __ = (function () {

	'use strict';

	/**
	 * Create the constructor
	 * @param {String} selector The selector to use
	 */
	const Constructor = function (selector) {
		if (selector === 'document') {
			this.elems = [document];
		} else if (selector === 'window') {
			this.elems = [window];
		} else {
			this.elems = document.querySelectorAll(selector);
		}
		
         
        // Backward compatibility, some methods need to use it
        //便于prototype新增的方法使用（一些dom查询后会丢失链，所以需要$()方法重新挂载原型链 ）
        this.storeSelector = this; 

	};

	/**
	 * Instantiate a new constructor
	 */
	const $ = function (selector) {
		return new Constructor(selector);
	};


	/**
	 * Iterate over elements
	 */
	 Constructor.prototype.nodes = function (callback) {
		let { elems } = this;
		elems = Array.prototype.slice.call(elems);
		elems.forEach( function(element, index, array) {
			callback.call(element, element, index, array);
		});
		return this;  //如果return this则会返回undefined
	};


	/**
	 * Return a prototype that extending by adding new methods
	 */
	$.fn = Constructor.prototype;


	/**
	 * Other methods
	 */
	Constructor.prototype.defaultPlugin = function() {
		this.nodes( function(element, index) {
			element.style.background = '#f60';
		});
		return this;
	};


	return $;

})();


__('.app').defaultPlugin();  //生效


/**
 * Other methods
 */
__.fn.myPlugin = function( color ) {
	this.nodes( function(element, index) {
		element.style.background = color;
	});
	return this;
};

__('.app3').myPlugin( '#ddd' );  //生效
```


### 第五步2(不使用new关键字的写法测试)


```js
//【注意，此时$()内的this.elems将报错：Uncaught TypeError: Cannot set properties of undefined (setting 'elems')】



console.log( this ); //Window

const __ = (function () {

	'use strict';
	

	console.log( this ); //undefined

	/**
	 * Create the constructor
	 * @param {String} selector The selector to use
	 */
	const $ = function (selector) {
		console.log( this ); //undefined

		if (selector === 'document') {
			this.elems = [document];
		} else if (selector === 'window') {
			this.elems = [window];
		} else {
			this.elems = document.querySelectorAll(selector);
		}
		
	};

	/**
	 * Iterate over elements
	 */
	$.prototype.nodes = function (callback) {
		let { elems } = this;
		elems = Array.prototype.slice.call(elems);
		elems.forEach( function(element, index, array) {
			callback.call(element, element, index, array);
		});
		return this;  //如果return this则会返回undefined
	};


	/**
	 * Return a prototype that extending by adding new methods
	 */
	$.fn = $.prototype;


	/**
	 * Other methods
	 */
	$.prototype.defaultPlugin = function() {
		this.nodes( function(element, index) {
			element.style.background = '#f60';
		});
		return this;
	};


	return $;

})();

__('.app').defaultPlugin();  //报错：Uncaught TypeError: Cannot set properties of undefined (setting 'elems')
```


### 第五步3(不使用new关键字的写法测试)


```js
//【注意，此时this将undefined，造成无法执行函数】


//变量生命在开头
let elems;


/**
 * Create the constructor
 * @param {String} selector The selector to use
 */
const __ = function (selector) {
	
	console.log( 'this: ', this ); //undefined
	
	if (selector === 'document') {
		elems = [document];
	} else if (selector === 'window') {
		elems = [window];
	} else {
		elems = document.querySelectorAll(selector);
	}
	

};


/**
 * Return a prototype that extending by adding new methods
 */
__.fn = __.prototype;


/**
 * Other methods
 */
__.prototype.defaultPlugin = function() {
	const els = Array.prototype.slice.call(elems);
	els.forEach( function(element, index, array) {
		element.style.background = '#f60';
	});
	return this;
};

console.log( __('.app') );  //undefined

__('.app').defaultPlugin();  //报错：Cannot read properties of undefined (reading 'defaultPlugin')
```

### 第五步4(不使用new关键字的写法测试)

```js
//【正确的写法1:】



/**
 * Create the constructor
 * @param {String} selector The selector to use
 */
const __ = function (selector) {
	
	console.log( 'this: ', this ); //{}
	
	if (selector === 'document') {
		this.elems = [document];
	} else if (selector === 'window') {
		this.elems = [window];
	} else {
		this.elems = document.querySelectorAll(selector);
	}

};


/**
 * Return a prototype that extending by adding new methods
 */
__.fn = __.prototype;


/**
 * Other methods
 */
__.prototype.defaultPlugin = function() {
	const els = Array.prototype.slice.call(this.elems);
	els.forEach( function(element, index, array) {
		element.style.background = '#f60';
	});
	return this;
};

const a = new  __('.app');
console.log( a );  //{elems: NodeList(5)}

a.defaultPlugin();  //生效
```

### 第五步5(不使用new关键字的写法测试,模拟构造函数,将prototype的名称和方法应用到每个对象上)

```js
//【正确的写法2:】



const __ = (function () {

	'use strict';
	

    //变量声明在开头
    let elems;


	/**
	 * Create the constructor
	 * @param {String} selector The selector to use
	 */
	const $ = function (selector) {
		
		console.log( this ); //undefined
		
		if (selector === 'document') {
			elems = [document];
		} else if (selector === 'window') {
			elems = [window];
		} else {
			elems = document.querySelectorAll(selector);
		}


		//特别注意：如果没有下面的代码，原型链函数将报错：Uncaught TypeError: __(...).defaultPlugin is not a function
		//下面的代码是将原型链的方法叠加到每个对象上
		let result;
		const API = {};
		const fns = Object.values( $.prototype );
		Object.keys( $.prototype ).forEach(function( name, index ) {
			API[name] = fns[index];
		});
		

		const list = [].slice.call(elems);
		Object.keys(API).forEach(function(name, prototypeIndex) {


			//Step (1) 
			//-------
			// 给每个对象都增加一个方法（以JSON的形式，类似把构造函数名称增加进去，就可以当方法调用了）
			list[name] = function() {


				//Step (2)
				//-------
				//slice method can also be called to convert Array-like objects/collections to a new Array. 
				//You just bind the method to the object. The arguments inside a function is an example of an 'array-like object'.
				/*
				也可以调用 slice 方法将类似数组的对象/集合转换为新的数组。 您只需将方法绑定到对象。 函数内的参数是“类数组对象”的一个例子。
				什么是类数组对象：

				var ao1 = {length: 0},                     // like []
					ao2 = {0: 'foo', 5: 'bar', length: 6}; // like ["foo", undefined × 4, "bar"]	
							
				转化方法：
				var arr = Array.prototype.slice.call(ao1); // []
				*/
				const currentElementArgs = arguments;
				console.log('currentElementArgs: ', currentElementArgs );  // Arguments ['blue', callee: (...), Symbol(Symbol.iterator): ƒ]  (注意这个arguments的位置，获取的是list中的对象传过来的参数，不是forEach()的参数，位置非常重要)



				//Step (3)
				//-------
				result = [];
				list.forEach( function(element) {
					
					//Step (4)
					//-------
					const fnResult = fns[prototypeIndex].apply(element, currentElementArgs);
					result.push(fnResult);

				});
			}

		});

	

		//特别注意：这里如果指定返回值，不是默认返回this（前提是有new关键字）
		//它就不是一个正常的带有new关键字的构造函数
		//那就需要上面的代码将构造函数包裹进去才能使用
		return list;

	};



	/**
	 * Return a prototype that extending by adding new methods
	 */
	$.fn = $.prototype;


	/**
	 * Other methods
	 */
	$.prototype.defaultPlugin = function( color ) {
		//注意这里的this就是每个对象，而不是整个函数
		this.style.background = color;

	};

	$.prototype.myplugin = function() {
		//注意这里的this就是每个对象，而不是整个函数
		this.classList.add('new-class');
	};
	

	return $;

})();


console.log( __('.app') );  //NodeList(5) [a,b,c,d,e]
__('.app').defaultPlugin( 'blue' );  //生效
```











