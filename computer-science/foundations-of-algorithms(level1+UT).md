# 常见算法基础(Level 1 + Unit Testing)

## 目录

 - [返回最大和最小的数字](#返回最大和最小的数字)
 - [分割字符串](#分割字符串)
 - [驼峰命名转换](#驼峰命名转换)
 - [判断素数质数](#判断素数质数)
 - [有效的括号](#有效的括号)
 - [验证二叉搜索树](#验证二叉搜索树)
 - [字符串字符计数](#字符串字符计数)
 - [插入五](#插入五)
 - [将一个数组分割成多个二维数组的组合(自定义题目)](#将一个数组分割成多个二维数组的组合自定义题目)


### 返回最大和最小的数字
----

输入一串空格分隔的数字，返回最高和最低的数字。

#### 测试
```js
const chai = require("chai");
const assert = chai.assert;
chai.config.truncateThreshold=0; // 禁用截断"断言错误中实际值和预期值的长度阈值"

describe("Basic tests", () => {
  it("Testing for fixed tests", () => {
    assert.strictEqual(highAndLow("8 3 -5 42 -1 0 0 -9 4 7 4 -4"), "42 -9");   
  });
});
```


#### 基础解法：
```js
function highAndLow(numbers) {
  const resNumDefault = numbers.replace(/\s+(\W)/, ' ');
  const nums = numbers.split(' ');
  const max = Math.max.apply(null, nums);
  const min = Math.min.apply(null, nums);
  
  //const max = Math.max(...nums);
  //const min = Math.min(...nums);
  
  return [max,min].join(' ');
  
}
```

#### 使用循环比较法：
```js
function highAndLow(numbers) {

    var numArray = numbers.split(" ");
    var max = Number(numArray[0]);
    var min = Number(numArray[0]);
    var num;

    console.log( typeof max ); // number
    console.log( max ); // 2

    for (var i = 1; i < numArray.length; i++) {

        num = Number(numArray[i]);

        if (num > max) max = num;
        if (num < min) min = num;
    }

    return max + " " + min;
}

console.log( highAndLow( '2 1 8 -1 5' ) ); // 8 -1
```


#### 使用reduce()：
```js
function highAndLow(numbers){
  var arr = numbers.split(" ").map( (x) => parseInt(x) );
  var max = arr.reduce((previousValue, currentValue) => previousValue > currentValue ? previousValue : currentValue );
  var min = arr.reduce((previousValue, currentValue) => previousValue < currentValue ? previousValue : currentValue );
  return max+" "+min;
}

console.log( highAndLow( '2 1 8 -1 5' ) ); // 8 -1
```

#### 使用sort()升序：
```js
function highAndLow(numbers) {
    var n = numbers.split(' ').map(function (v) {
        console.log(parseInt(v)); 
        //分5次输出：
        // 2
        // 1
        // 8
        // -1
        // 5

        return parseInt(v);
    }).sort(function (a, b) { //sort()方法进行排序, 要比较数字而非字符串，比较函数可以简单的以 a 减 b，如下的函数将会将数组升序排列
        console.log(a, b);
        // 1 2
        // 8 1
        // 8 2
        // -1 2
        // -1 1
        // 5 2
        // 5 8

        return a - b;  // a - b小于0，则a会排列到b前面
    });

    return n.length > 1 ? n.pop() + ' ' + n.shift() : n[0] + ' ' + n[0];
}

console.log( highAndLow( '2 1 8 -1 5' ) ); // 8 -1
```

#### 使用冒泡排序：

```js
function highAndLow(numbers) {
    let arr = numbers.split(' ').map(x => Number.parseInt(x))

    const swap = (i, j) => {
        let temp;
        temp = arr[j];
        arr[j] = arr[i];
        arr[i] = temp;
    }

    let j = 1
    for (let i = 0; i < arr.length; i++) {
        if (arr[i] > arr[j]) {
            swap(i, j)
            i -= 2
            j -= 2
        }
        j++
    }

    return `${arr[arr.length - 1]} ${arr[0]}`
}

console.log( highAndLow( '2 1 8 -1 5' ) ); // 8 -1


// 结果可使用slice截取：
function highAndLow(numbers) {
    let arrays = numbers.split(' ');

    //相当于循环5+5=10次
    for (var i = 0; i < arrays.length - 1; i++) {
        for (var j = 0; j < arrays.length - 1 - i; j++) {    // j < arrays.length - 1 也成立
   
            if (arrays[j] > arrays[j + 1]) {
                var t = arrays[j + 1];
                arrays[j + 1] = arrays[j];
                arrays[j] = t;
            }
        }
    }

    return `${arrays[arrays.length - 1]} ${arrays[0]}`
    // return `${arrays.slice(-1)} ${arrays.slice(0, 1)}`;
}

console.log( highAndLow( '2 1 8 -1 5' ) ); // 8 -1
```


### 分割字符串
----

将字符串拆分为两个字符对。 如果字符串包含奇数个字符，那么它应该用下划线 ('_') 替换最后一对中缺少的第二个字符。

#### 测试
```js
const { assert } = require('chai');

describe("Split Strings", () => {
  it("Basic tests", () => {
    assert.deepEqual(solution("abcdef"), ["ab", "cd", "ef"]);
    assert.deepEqual(solution("abcdefg"), ["ab", "cd", "ef", "g_"]);
    assert.deepEqual(solution(""), []);
  });
});
```



#### 基础解法：

使用for...in和charAt()

```js
function solution(str){
   if ( typeof(str) !== 'string' ) return;
   
  let lastStr = '';

   const res = [];
  
   let item = '';
   for (let i in str) {
     item += str[i];
     
     if (i % 2) {
       res.push(item);
       item = '';
     }
   }
    
   //Judge whether the length is even
   if (str.length % 2 !== 0) {
	   let lastStr = str.charAt(str.length-1) + '_';
	   res.push(lastStr);
   }
  
  return res;
}
```


#### 使用正则：

符号用来查找除了换行符和行结束符以外的任意单个字符。

```js
// {2} 匹配2次
function solution(s){
   return (s+"_").match(/.{2}/g) || []
}
```

或者：
```js
// {1,2} 最少匹配1次，最多匹配2次

function solution(str){
  if (str.length === 0) return []
  
  return str.match(/.{1,2}/g).map(c => c.length < 2 ? c.concat("_") : c)
}
```


#### 使用while循环
```js
function solution(str){
  var i = 0;
  var result = new Array();
  if (str.length % 2 !== 0) {
    str = str + '_';
  }
  while (i < str.length) {
      result.push(str[i] + str[i+1]);
      i += 2;
    }
  return result;
}
```

#### 使用索引+1
```js
function solution(str){
  let res = [];
  for (var i = 0; i < str.length; i +=2) res.push(`${str[i] + (str[i+1] || '_')}`)
   return res;
}
```

#### 使用splice，map和filter

splice() 方法用于添加或删除数组中的元素。

注意：这种方法会改变原始数组。
```js
var fruits = ["Banana", "Orange", "Lemon", "Apple", "Mango"]
console.log(fruits.splice(0,1));  // ['Banana']
console.log(fruits);           // ['Orange', 'Lemon', 'Apple', 'Mango']


function solution(str) {
  const arr = str.split('');

  return arr
    .map((el, i) => el + (arr.splice(i + 1, 1)[0] || '_'))
    .filter(el => el);
}
```


#### 使用slice和while

slice()省略第二个参数

注意：slice不改变元数组

```js
var fruits = ["Banana", "Orange", "Lemon", "Apple", "Mango"]
console.log(fruits.slice(1));  // ['Orange', 'Lemon', 'Apple', 'Mango']
console.log(fruits);           // ["Banana", "Orange", "Lemon", "Apple", "Mango"]
	
//----

function solution(str){
  var evenStr = str.length % 2 === 0 ? str : `${str}_`;  
  var result = [];
  
  while (evenStr.length >= 2) {
    let stringPair = evenStr.slice(0, 2);
    let newStr = evenStr.slice(2);
    result.push(stringPair);
    evenStr = newStr;
  }
  
  return result;
  
}
```

#### 使用for...of和Array.form()
```js
console.log(Array.from('foo'));
// expected output: Array ["f", "o", "o"]

console.log(Array.from([1, 2, 3], x => x + x));
// expected output: Array [2, 4, 6]

//--------

function solution(str) {
    // return an empty array if the string is empty as well.
    if (!str) {
        return [];
    }
    
    const strArray = Array.from(str);

    const result = [];
    let currentItemToPushIntoResultArray = "";
    
    // is there a better way to "divide" the Array into multiple "even items" (other than using an explicit loop)?
    for (const char of strArray) {
        if (currentItemToPushIntoResultArray.length === 2) {
            result.push(currentItemToPushIntoResultArray);
            currentItemToPushIntoResultArray = "";
        }

        currentItemToPushIntoResultArray += char;
    }

    if (currentItemToPushIntoResultArray) {
        // if we still have some data being pointed by the variable above,
        // it means that some values are still to be added to the result array.
        // so just add it and move on.
        result.push(currentItemToPushIntoResultArray);
    }

    // if the last item has only one char;
    if (result[result.length - 1].length === 1) {
        // append it with an underscore.
        result[result.length - 1] += "_";
    }
    
    return result;
}
```


#### 使用charAt()和索引+1

```js
function solution(str){
  let result = []
  if (str.length % 2 == 1) {
    str += "_"
  }

  for (let i = 0; i < str.length; i += 2) {
    result.push(str.charAt(i) + str.charAt(i + 1))
  }
  
  return result
}
```

#### 使用ceil() -上舍入 和 padEnd()

Math.ceil(5/2)　　// 3  向上取整,有小数就整数部分加1

Math.floor(5/2)  // 2 向下取整

Math.round(5/2)  // 3 四舍五入


JS字符串补全方法padStart()和padEnd()

```js
function solution(str){
  const newArr = new Array(Math.ceil(str.length / 2));
  for (let i = 0; i < newArr.length; i++) {
    newArr[i] = (str.charAt(2 * i) + str.charAt(2 * i + 1)).padEnd(2, "_");
  }
  return newArr;
}
```


#### 使用substr() 和while
```js
function solution(str) {
  let result = [];
  while (str.length > 1) {
    result.push(str.substr(0, 2));
    str = str.substr(2);
  }
  if (str.length === 1) result.push(str + '_');
  return result;
}
```


#### 使用reduce()和数组的pop()

```js
function solution(str){
  if (!str) {
    return [];
  }
  
  let res = str;
  if (str.length % 2 === 1) {
    res = `${str}_`;
  }

  return res.split('').reduce((acc, cur, idx) => {
    if (acc.length === 0 || acc[acc.length - 1].length === 2) {
      return [...acc, cur]
    } 
    
    const lastVal = acc.pop();
    return [...acc, `${lastVal}${cur}`];
  }, []);
}
```


#### 使用字符串的concat()拼接和正则

```js
function solution(str){
  const last = (str.length % 2 == 1) ? [str[str.length - 1] + '_'] : [];  
  var res = str.match(/[a-zA-Z]{2}/g);
  res = res ? res : []
  return res.concat(last);
}
```


#### 使用slice()

```js
function solution(str) {
	let arr = [];
	let n = str.length;
	if (n % 2 == 0) {
		for (let i = 0; i < n; i += 2) arr.push(str.slice(i, i + 2));
	} else {
		for (let j = 0; j < n - 2; j += 2) arr.push(str.slice(j, j + 2));
		arr.push(str[n - 1] + '_');
	}
	return arr;
}
```


### 驼峰命名转换
----

将破折号/下划线分隔的单词转换为驼峰式大小写。 仅当原始单词大写时，输出中的第一个单词才应大写（称为 Upper Camel Case，通常也称为 Pascal 大小写）。


#### 测试
```js
describe("Tests", () => {
  it("test", () => {
    Test.assertEquals(toCamelCase(''), '', "An empty string was provided but not returned")
    Test.assertEquals(toCamelCase("the_stealth_warrior"), "theStealthWarrior", "toCamelCase('the_stealth_warrior') did not return correct value")
    Test.assertEquals(toCamelCase("The-Stealth-Warrior"), "TheStealthWarrior", "toCamelCase('The-Stealth-Warrior') did not return correct value")
    Test.assertEquals(toCamelCase("A-B-C"), "ABC", "toCamelCase('A-B-C') did not return correct value")

    });
});
```



#### 基础解法：

```js
function toCamelCase(str){
  if ( typeof str !== 'string' || str === '' ) return '';
  str = str.replace(/\_/g, '-');
  const strArr = str.split('-');
  
  const newArr = strArr.map((item,index)=> {
    const firstWord = item.substring(0,1);  //也可使用slice
    
    if (index === 0) {
      return firstWord + item.substring(1);
    } else {
      return firstWord.toUpperCase() + item.substring(1);
    }
    
  });
  
  return newArr.join('');
  

}
```

#### 使用正则和replace返回函数
```js
function toCamelCase(str){
      var regExp=/[-_]\w/ig;
      return str.replace(regExp,function(match){
            return match.charAt(1).toUpperCase();
       });
}


//--------

function toCamelCase(str){
  return str.replace(/[-_](.)/g, (_, c) => c.toUpperCase());
}


//--------

function toCamelCase(str) {
  return str.replace(/[_-][A-Za-z]/g, function(match) {return match[1].toUpperCase();});
}
```

#### 使用charAt()和slice()

```js
function toCamelCase(str){
  return str.split(/-|_/g).map((w, i) => (i > 0 ? w.charAt(0).toUpperCase() : w.charAt(0)) + w.slice(1)).join('');
}
```

#### 使用reduce()
```js
function toCamelCase(str){
  return str.split(/\-|_/).reduce(function(previous, current, index){ return camelize(previous, current, index);});
}

function camelize(previous, current, index){
  return previous + current.capitalizeFirstLetter();
}

String.prototype.capitalizeFirstLetter = function() {
    return this.charAt(0).toUpperCase() + this.slice(1);
}
```


#### 使用split()的正则复合分割

```js
// 原理：

const str = 'The-quick-brown fox jumps over the lazy dog.';
const strCopy = str.split(/-|_/)
console.log(strCopy); // ["The", "quick", "brown fox jumps over the lazy dog."]


const str2 = 'The-quick-brown_fox_jumps over the lazy dog.';
const strCopy2 = str2.split(/-|_/)
console.log(strCopy2); // ["The", "quick", "brown", "fox", "jumps over the lazy dog."]



function toCamelCase(str){
  if (str.length === 0) return str;
  let firstUpperCase = false;
  let res = "";

  if (str[0] === str[0].toUpperCase()) firstUpperCase = true;

  let arrStr = str.split(/-|_/)

  for (let i = 0; i < arrStr.length; i++) {
    if (i===0 && !firstUpperCase) {
      res += arrStr[i];
    } else {
      res += capitilizeFirstLetter(arrStr[i]);
    }
  }
  
  return res;
}

function capitilizeFirstLetter (string) {
  return string[0].toUpperCase() + string.slice(1)
}
```


#### 使用split()的正则复合分割和...解构

```js
// 原理：

const str = 'The-quick-brown_fox jumps over the lazy dog.';
const [first, ...others] = str.split(/-|_/gi);
console.log(first); // "The"
console.log(others); // ["quick", "brown", "fox jumps over the lazy dog."]




function toCamelCase(str){
  if (str) {
    const [first, ...others] = str.split(/-|_/gi);
    const othersArr = [];
    for (let word of others) {
        word = word.replace(word[0], word[0].toUpperCase());
        othersArr.push(word);
    }
    return `${first[0] === first[0].toUpperCase() ? first[0].toUpperCase() + first.slice(1) : first}${othersArr.toString().replace(/,/g, '')}`;
    } else {
    return '';
  }
}
```

#### 使用includes()

```js
function toCamelCase(str){
  var newArray = []
  str.includes("-")? newArray = str.split("-") : str.includes("_")? newArray = str.split("_"): null
  for(i=1;i<newArray.length;i++){
    newArray[i] = newArray[i].charAt(0).toUpperCase() + newArray[i].slice(1)
  }
  return newArray.join("")
}
```




### 判断素数质数
----

Z+中的一个数P>1, 若满足整除P的正整数只有P和1，则称为素数。如2，3，5，7，11，13...

#### 测试
```js
describe("Basic", ()=>{
	
	it("Basic tests", () => {
		
		Test.assertEquals(isPrime(0),  false, "0 is not prime");
		Test.assertEquals(isPrime(1),  false, "1 is not prime");
		Test.assertEquals(isPrime(2),  true, "2 is prime");
		Test.assertEquals(isPrime(73), true, "73 is prime");
		Test.assertEquals(isPrime(75), false, "75 is not prime");
		Test.assertEquals(isPrime(-1), false, "-1 is not prime");
		
	});
	
	it("Test prime", () => {
		
		Test.assertEquals(isPrime(3),  true, "3 is prime");
		Test.assertEquals(isPrime(5),  true, "5 is prime");
		Test.assertEquals(isPrime(7),  true, "7 is prime");
		Test.assertEquals(isPrime(41), true, "41 is prime");
		Test.assertEquals(isPrime(5099), true, "5099 is prime");
		
	});
	
	it("Test not prime", () => {
		
		Test.assertEquals(isPrime(4),  false, "4 is not prime");
		Test.assertEquals(isPrime(6),  false, "6 is not prime");
		Test.assertEquals(isPrime(8),  false, "8 is not prime");
		Test.assertEquals(isPrime(9), false, "9 is not prime");
		Test.assertEquals(isPrime(45), false, "45 is not prime");
		Test.assertEquals(isPrime(-5), false, "-5 is not prime");
		Test.assertEquals(isPrime(-8), false, "-8 is not prime");
		Test.assertEquals(isPrime(-41), false, "-41 is not prime");
		
	});
});
```
#### 基础解法：

注意要做时间复杂度优化，否则超时

下面是可能超时的写法：

```js
function isPrime(num) {
  if ( isNaN( num ) || !isFinite( num ) ) return false;
  if ( num < 2 ) return false;

  
  for (let i=2; i < num;i++) {
    if (num % i === 0) {
      return false;
      break;
    }  
  
  }
  

  return true;

}

//-------

function isPrime(num) {
  if ( isNaN( num ) || !isFinite( num ) ) return false;
  if ( num < 2 ) return false;


  //依次判断是否能被奇数整除，最大循环为数值的开方
  var squareRoot = Math.sqrt(num);
  //因为2已经验证过，所以从3开始；且已经排除偶数，所以每次加2
  for(var i = 3; i <= squareRoot; i += 2) {
    if (num % i === 0) {
       return false;
    }
  }
  
  for (let i=2; i < num;i++) {
    if (num % i === 0) {
      return false;
      break;
    }  
  
  }
  

  return true;

}
```


#### 使用Math.sqrt()平方根

做时间复杂度优化的写法【推荐学习】：

```js
function isPrime(num) {
  if (num < 2) return false;
  const limit = Math.sqrt(num);
  for (let i = 2; i <= limit; ++i) {
    if (num % i === 0) {
      return false;
    }
  }
  return true;
}
```



#### 使用Math.pow(底数,几次方)
```js
function isPrime(n) {
  if (n <= 1)
    return false;
  if (n <= 3 || n == 5 || n == 7 || n == 11)
    return true;
  if (n % 2 === 0 || n % 3 === 0)
    return false;
  for (var i = 5; i <= Math.pow(n, 0.5); i += 6)
    if (n % i === 0 || n % (i + 2) === 0)
      return false;
  return true;
}
```

#### 使用简化的时间复杂度的for

x**n  相当于 Math.pow(x,n)  即x的n次方

一个*是乘  两个*就是乘方了

```js
const isPrime = num => {
  for (let i = 2; i <= num ** .5; i++) {
    if (!(num % i)) return false;
  }
  return num > 1;
}
```

#### 使用every()方法
```js
function isPrime(num) {
  let n=Math.sqrt(num);
  const arr=[];
  for(let j=0; j<n+1; j++){
	  arr.push(j);
  }
  //TODO
  return num > 1 && arr.every(i => num === i || num % i);
}

console.log(isPrime(55789));
```

#### 使用filter()方法
```js
const isPrime = n => {
  if (n < 2) return false
  const sqrt = Math.floor(Math.sqrt(n))
  return Array.from({length: sqrt}, (_,i) => i + 2)
        .filter(v => n % v === 0)
        .length < 1 || n === 2
}
```



### 有效的括号
----

编写一个函数，该函数接受一串括号，并确定括号的顺序是否有效。

#### 测试
```js
const { assert } = require('chai');

describe("Tests", () => {
  it(`values: "("`, () => assert.strictEqual(validParentheses( "(" ), false));
  it(`values: ")"`, () => assert.strictEqual( validParentheses( ")" ), false));
  it(`values: ""`, () => assert.strictEqual(validParentheses( "" ), true));
  it(`values: "()"`, () => assert.strictEqual(validParentheses( "()" ), true));
  it(`values: "())"`, () => assert.strictEqual(validParentheses( "())" ), false));
});
```


#### 基础解法：

**原理：**

 - 1.将字符串拆成两个部分
 - 2.第一部分入栈
 - 3.第二部分判断是否和第一部分匹配

```js
const validParentheses = (s) => {
  if (s.length % 2 !== 0) return false;
  
  const stack = [];
  const map = new Map([
    ['(', ')'],
    ['[', ']'],
    ['{', '}']
  ]);
    
  for (let i = 0 ; i < s.length ; i += 1) {  // 这里的 i+=1 等同于 i++
    if (map.has(s[i])) {
      stack.push(map.get(s[i]));
	  console.log( map.get(s[i]), ' 对应 ', s[i] );  // )  对应  (
    } else if (s[i] !== stack.pop()) {   //pop删除数组的最后一个元素并返回删除的元素, 只要运行s[i] !== stack.pop()条件，不论结果如何，pop()就被执行
      return false;
    } 
  }
  return stack.length === 0;   //也可以写成 return true;
};


console.log( validParentheses( "()" )); //true
console.log( validParentheses( "()[]{}" ));//true
console.log( validParentheses( "(]" ));//false
console.log( validParentheses( "([)]" ));//false
console.log( validParentheses( "{[]}" ));//true
```

#### 使用for循环：
```js
function validParentheses(parens){
  var n = 0;
  for (var i = 0; i < parens.length; i++) {
    if (parens[i] == '(') n++;
    if (parens[i] == ')') n--;
    if (n < 0) return false;
  }
  
  return n == 0;
}

//-----

function validParentheses(parens){
  var indent = 0;
  
  for (var i = 0 ; i < parens.length && indent >= 0; i++) {
    indent += (parens[i] == '(') ? 1 : -1;    
  }
  
  return (indent == 0);
}
```


#### 使用数组：
```js
function validParentheses(s) {
   let st = [];
   const legend = {
     '(': ')',
     '{': '}',
     '[': ']'
   };

   for (let i = 0; i < s.length; i++) {
     if (s[i] === '(' || s[i] === '{' || s[i] === '[') {
       st.push(s[i]);
     } else if (legend[st.pop()] !== s[i]) {
       return false;
     }

   }
   return st.length ? false : true;
}
```


### 验证二叉搜索树
----

给定二叉树的根，确定它是否是有效的二叉搜索树 (Binary Search Tree, BST)。

**一个有效的 BST 定义如下：**

 - 节点的左子树仅包含键小于节点键的节点。
 - 节点的右子树仅包含键大于节点键的节点。
 - 左右子树也必须是二叉搜索树。 

```base
示例 1：

容器

输入：root = [2, 1, 3]
输出：真


    2
   / \
  1   3



示例 2：

容器

输入：root = [5, 1, 4, null, null, 3, 6]
输出：假
解释：根节点的值为 5，但其右子节点的值为 4。

    5
   / \
  1   4
    /  \
   3    6

```


**约束**

 - 树中的节点数在 [1, 10^4] 范围内。
 - -2^31 <= Node.val <= 2^31 - 1


**解释**

 - 不正确的做法：我们大多数人会想到的第一个天真的方法是检查每个节点，左孩子应该更小，右孩子应该更大。
 - 正确的做法：需要跟踪其左右子树中任何节点的最大值和最小值。


#### 测试
```js
const assert = require('chai').assert;

describe('example test cases', () => {
  // Convenience helper to construct a tree.
  const Tree = (value, left, right) => new Node(value, left, right);
  
  it('should validate the examples', () => {
    assert.strictEqual(isBST(Tree(5, Tree(2, Tree(1), Tree(3)), Tree(7, null, Tree(9)))), true);
    assert.strictEqual(isBST(Tree(2, Tree(1), Tree(3))), true);
    assert.strictEqual(isBST(Tree(1, Tree(2), Tree(3))), false);
    assert.strictEqual(isBST(Tree(5, Tree(2, null, Tree(7)), Tree(9))), false);
  });
});
```

#### 过程：
```js
/*
过程演示：

root = [2, 1, 3]  根节点为2

// 初次递归，初始化一个最小值和最大值，使二叉树永远成立
第1步: isBSTUtil(root, min, max)

// 如果根节点不存在，返回false
第2步: if root == NULL
       return false

第3步: if 2 <= min || 2 >= max
       return false
   

//递归每个子节点1和3
第4步:  return (isBSTUtil(1, min, 2) && isBSTUtil(3, 2, max))

第5步: 递归 isBSTUtil(1, min, 2)
       ...过程省略

第6步: 递归 isBSTUtil(3, 2, max)
       ...过程省略

第7步: 重复上面的步骤，知道递归传入的值为 null，那么将结束递归，如
       return isBSTUtil(null, min, ?) && isBSTUtil(null, ?, max)


提示：

// 不要使用 MIN_VALUE 和 MAX_VALUE
console.log( Number.MIN_VALUE );  // 5e-324
console.log( Number.MIN_SAFE_INTEGER );  // -9007199254740991
console.log( Number.MAX_VALUE );  // 1.7976931348623157e+308
console.log( Number.MAX_SAFE_INTEGER );  // 9007199254740991


*/
```


#### 基础解法：
```js
// 助手类，Node类用来创建一个二叉树
class Node {
    constructor(value, left = null, right = null) {
        this.value = value;
        this.left = left;
        this.right = right;
    }
}

function isBSTUtil(node, min, max) {
    //一个空树
    if (node == null) return true;

    // 如果此节点违反最小/最大约束
    if (node.value < min || node.value > max) return false;

    // 也可简写成 return (isBSTUtil(node.left, min, node.value) && isBSTUtil(node.right, node.value, max));
    // 如果要变成左节点大于父节点，右节点小于父节点，
    // 则改为 isBSTUtil(node.left, node.value, max) === true && isBSTUtil(node.right, min, node.value) === true
    if ( isBSTUtil(node.left, min, node.value) === true && isBSTUtil(node.right, node.value, max) === true ) {
        return true;
    } else {
        return false;
    }
}

function isBST(root) {
    return isBSTUtil(root, Number.MIN_SAFE_INTEGER, Number.MAX_SAFE_INTEGER);
}




//第一种用法：
//------------
let root;
root = new Node(4);
root.left = new Node(2);
root.right = new Node(5);
root.left.left = new Node(1);
root.left.right = new Node(3);
console.log(isBST(root));  // true
console.log(root);



//第二种用法：
//------------
// 包装一个树的生成器
const Tree = (value, left, right) => new Node(value, left, right);

const tree1 = Tree(5, Tree(2, Tree(1), Tree(3)), Tree(7, null, Tree(9)));
const tree2 = Tree(3, Tree(1), Tree(2));

console.log( isBST(tree1) );  // true
console.log( isBST(tree2) );  // false
    



/*

[4, 2, 5, 1, 3, null, null]

           4
         /   \
        2     5
       /  \  /  \
      1   3  n   n
     / \ / \
    n  n n  n
     

输出：
{
   "data":4,
   "left":{
      "data":2,
      "left":{
         "data":1,
         "left":null,
         "right":null
      },
      "right":{
         "data":3,
         "left":null,
         "right":null
      }
   },
   "right":{
      "data":5,
      "left":null,
      "right":null
   }
}
*/

```

#### 使用every()方法

将每个元素都遍历递归成1个数组，然后根据数组的索引来进行相邻对比。

按照此原理，可以使用数组的其它方法进行变形。见解法3。


```js
//扁平化树
function flattenTree(node) {
    if (node) {
        //中序遍历(节点本身在中间，左右节点在两边)
        return [...flattenTree(node.left), node.value, ...flattenTree(node.right)];
    } else {
        return [];
    }
}

/*

[5, 2, 7, 1, 3, null, 9]

           5
         /   \
        2     7
       /  \  /  \
      1   3  n   9
     / \ / \     / \
    n  n n  n    n  n
     
将被扁平为 

[1, 2, 3, 5, 7, 9]


*/


function isBST(root) {
    const values = flattenTree(root);

    //every() 都满足条件才返回true，false可跳出循环
    //可以简写成 return values.ever... || values.ever...;
    if (values.every((value, index, arr) => index === 0 || value > arr[index - 1]) === true) {
        return true;
    } else {
        return false;
    }
}
```



#### 使用slice()和sort()

```js
//扁平化树(使用concat)
function flattenTree(node) {

    //中序遍历
    return (node.left === null ? [] : 
            flattenTree(node.left))
            .concat(node.value)
            .concat(node.right === null ? [] : flattenTree(node.right));
}
function isEqual(a, b) {
    return JSON.stringify(a) == JSON.stringify(b);
}

function isBST(root) {
    const values = flattenTree(root);
    const sorted = values.slice().sort((a,b) => b-a);
    return isEqual(values, sorted.reverse());
}

```




### 字符串字符计数
----

计算字符串中所有出现的字符。 如果你有一个像 aba 这样的字符串，那么结果应该是 {'a': 2, 'b': 1}。
如果字符串为空，那么结果应该是空的对象字面量，{}。

#### 测试
```js
const chai = require("chai");
const assert = chai.assert;
chai.config.truncateThreshold=0; // 禁用截断"断言错误中实际值和预期值的长度阈值"


describe("Tests", () => {
  it("should pass sample tests", () => {
    assert.deepEqual(count("aba"), { a: 2, b: 1 }); 
    assert.deepEqual(count(""), {});    
  });
});


```


#### 基础解法：

使用Arrayfrom()和hasOwnProperty()

```js
function count(string) {
    const res = {};
    const strArr = Array.from(string);
    let count = 1;
    strArr.forEach(function (item, indx) {
        if (res.hasOwnProperty(item)) {
            count++;
        } else {
            count = 1;
        }
        res[item] = count;

    });

    return res;
}
```


#### 直接判断JSON键值是否存在累加


```js
function count (string) {  
  var count = {};
  string.split('').forEach(function(s) {
     count[s] ? count[s]++ : count[s] = 1;
  });
  return count;
}
```



#### 使用reduce()

```js
function count (string) {
  return string.split('').reduce(function(counts,char){
    counts[char] = (counts[char]||0) + 1;
    return counts;
  },{});
}

```


```js
function count (string) {  
  if (!string) return {};
  return string.toLowerCase().replace(/[^a-z]/gi, '').split('').reduce( (ac, item) => {
     ac[item] =  ac[item]? ac[item] + 1: 1;
     return ac;
  }, {});
}
```


使用in关键字判断JSON键值存在

```js
function count (string) {  
  return string.split("").reduce(function(obj, elem) {
    if (elem in obj)
      obj[elem]++;
    else
      obj[elem] = 1;
    return obj;
  }, {});
}

```



#### 使用正则统计字符数

```js
function count (string) {  
  var string1 = string.split("")
  var obj = {}
  for (var i =0; i < string.length; i++) {
  var checkForDigit = new RegExp (`${string1[i]}` , 'g')
    var matchedAmount = string.match(checkForDigit).length
    var count = matchedAmount
    obj[`${string1[i]}`] = count
  }
  // The function code should be here
   return obj;
}

```



#### 使用for...of

```js
function count (string) {  
  // The function code should be here
  let obj = {}
  for(item of string){
    if(!obj[item]){
      obj[item] = 0;
    }
    obj[item]++;
  }
    
  return(obj)
//    return {};
}
```




### 插入五
----

给定一个数字，在数字的任意位置插入一个5，使得插入后的这个数字最大。

#### 测试
```js
const chai = require("chai");
const assert = chai.assert;
chai.config.truncateThreshold=0; // 禁用截断"断言错误中实际值和预期值的长度阈值"

describe("Basic tests", () => {
  it("Testing for fixed tests", () => {
    assert.strictEqual(insertFive(234), 5234);
    assert.strictEqual(insertFive(73), 753);
    assert.strictEqual(insertFive(-12), -125);  // 插入末尾
    assert.strictEqual(insertFive(-78), -578);
  });
});

```


#### 解法：
```js
function insertFive(a) {
	// 先把a的绝对值求出来，然后转为字符串
	const b = Math.abs(a);
	let strArr = b.toString().split('');

	// 取出a的位数
	const len = strArr.length;

	
	if (a >= 0) {
		console.log('正数');
		for (let i = 0; i < len; i++) {
			if (strArr[i] <= '5') {
				// 在位置i，删除0个元素，添加1个元素"5"：
				strArr.splice(i, 0, '5');
				break;
			}
		}

		
	} else {
		console.log('负数');
		for (let i = 0; i < len; i++) {
			if (strArr[i] >= '5') {
				// 在位置i，删除0个元素，添加1个元素"5"：
				strArr.splice(i, 0, '5');
				break;
			}
		}
	}



	// 这里如果发现strArr的长度没有变化，说明5没有被插入进去，则添加到末尾
	if (strArr.length == len) {
		console.log('插入末尾');
		strArr.splice(len, 0, '5');
	}

	//console.log('strArr.join(""): ', strArr.join(''));

	const res = parseInt( strArr.join('') );
	return a >= 0 ? res : -res;
}
```

注：算法中的所有 '5' 均可改为纯数字类型 5 (不加引号)






### 将一个数组分割成多个二维数组的组合(自定义题目)
----


将一个数组分割成多个二维数组的组合，例如['a','b','c','d','e','f']将分割成  [["a","b"],["c","d"],["e","f"]]

#### 测试
```js
const chai = require("chai");
const assert = chai.assert;
chai.config.truncateThreshold=0; // 禁用截断"断言错误中实际值和预期值的长度阈值"


describe("Tests", () => {
  it("should pass sample tests", () => {
    assert.deepEqual(arrayConversion(['a','b','c','d','e','f']), [["a","b"],["c","d"],["e","f"]]); 
    assert.deepEqual(arrayConversion(['a','b','c','d']), [["a","b"],["c","d"]]);    
  });
});
```


#### 索引填充：
```js
function arrayConversion(arr) {
  //数学计算，索引满足等差数列
  return Array.from({ length: arr.length/2 }).map( (item, i) => [arr[2*(i)], arr[2*(i+1)-1]]);
}

```



#### 正则匹配：
```js
function arrayConversion(arr) {
  return JSON.stringify(arr).match(/"(.*?)","(.*?)"/g).map(x => {
      //过滤一下分组后的item
      return x.replace(/"(.*?)"/g, '$1').split(',')
  });
}

```




#### 增量分割：
```js
function arrayConversion(arr) {
    return arr.reduce((acc, cur, i) => {
        // (1) 初次调用的返回值acc为初始值 []，它是一个数组，所以将 cur 元素合并进去
        // (2 )每次执行 acc.pop()后，如果acc依然是个数组，而不是字符串，就将 cur 元素合并进去
        if (acc.length === 0 || acc[acc.length - 1].length === 2) {
            return [...acc, cur];
        }

        //取出那个数组和字符串混合的字符串元素，组合成一个新的二维数组(完成分组)
        const lastVal = acc.pop();
        return [...acc, [lastVal, cur]];
    }, []);
}
```


#### 减量分割：
```js
function arrayConversion(arr) {
    return arr.map((item, i) => {
        if (i < 2) arr.push(null);  //由于每次减少两个元素，最终结果想要循环 arr.length/2 次，就需要在末尾处增加2个空元素
        return arr.splice(0, 2);   //每次减少两个元素并覆盖原数组
    }).filter(x => x !== null);
}

```


#### 矩阵法：

前面四种方法都是从过程入手，逐一求得最终结果，那么换种思路，从结果入手，我们先来创造一个想要的结果的模版


```js
function arrayConversion(arr) {

    // 创建一个 3x2 的辅助矩阵，用来记忆结果
    const row = arr.length / 2;
    const column = 2;
    const matrix = Array(row).fill(0).map(x => Array(column).fill(0));

    // 把矩阵的坐标和原数组的索引一一对应即可, 矩阵的坐标为 matrix[x][y]
    for (let i = 0; i < row; i++) {
        for (let j = 0; j < column; j++) {
            matrix[i][j] = arr[j + i * 2]; // 将笛卡尔坐标系转化成一维数组的角标
        }
    }

    return matrix;
}

```
