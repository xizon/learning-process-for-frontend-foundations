# 常见算法基础(Level 1) 

## 目录

 - [递归函数逻辑](#递归函数逻辑)
 - [冒泡排序](#冒泡排序)
 - [选择排序](#选择排序)
 - [插入排序](#插入排序)
 - [希尔排序](#希尔排序)
 - [数组去重](#数组去重)
 - [判断质数](#判断质数)
 - [缓动算法-趋近10](#缓动算法-趋近10)
 - [缓动算法-趋近0](#缓动算法-趋近0)
 - [计算坐标的弧度角度](#计算坐标的弧度角度)
 - [斐波那契数列](#斐波那契数列)
 - [翻转(反转)单链表](#翻转反转单链表)
 - [设计LRU](#设计lru)
 - [两个栈实现队列](#两个栈实现队列)
 - [二叉树层序遍历](#二叉树层序遍历)
 - [无重复最长子串](#无重复最长子串)

 

### 递归函数逻辑
----
效率比迭代(循环)方法效率要低，但是更加容易理解，写法简单

**阶乘**
```js
function Fn(x) {
    if (x === 1 || x === 0) {  //基线条件（非常重要）
	return 1;
    }
	
    return x*Fn(x-1);
}

console.log( Fn(1) ); // 1
console.log( Fn(2) ); // 2	
console.log( Fn(5) ); // 120

/*
过程：
！！！！ Fn(5)的计算逻辑如下：最后到了基线条件就完成了最终计算结果
(1) Fn(5) = 5 * Fn(4)
(2) Fn(5) = 5 * (4 * Fn(3))
(3) Fn(5) = 5 * 4 * (3 * Fn(2))
(4) Fn(5) = 5 * 4 * 3 * (2 * Fn(1))
(5) Fn(5) = 5 * 4 * 3 * 2 * (1)
*/
```

**斐波那契数列**

类似数列：0 、 1 、 1 、 2 、 3 、 5 、 8 、13、21、34、……
```js
function Fn(x) {
    if (x < 1) return 0;  //基线条件（非常重要）
    if (x < 2) return 1;  //基线条件（非常重要）

    return Fn(x-1) + Fn(x-2);	
}

console.log( Fn(1) ); // 1
console.log( Fn(2) ); // 1	
console.log( Fn(5) ); // 5
console.log( Fn(6) ); // 8		
console.log( Fn(7) ); // 13
console.log( Fn(30) ); // 832040

/*
过程：
！！！！ Fn(5)的计算逻辑如下：最后到了基线条件就完成了最终计算结果

                     Fn(5)
                  /        \
               Fn(4)  +     Fn(3)
               /    \       /    \
             Fn(3) + Fn(2) Fn(2) + Fn(1)
             /    \    
         Fn(2) + Fn(1)

最终结果其实是：  Fn(2) + Fn(1) + Fn(2) + Fn(2) + Fn(1) = 1 + 1 + 1 + 1 + 1 = 3
*/
```

### 冒泡排序
----

```js
/*
元素替换原理：

var arr2 = [3,6,4,2,11,10,5];
arr2[1] = arr2[0];
console.log( arr2 ); // [3, 3, 4, 2, 11, 10, 5]

var arr3 = [3,6,4,2,11,10,5];
var temp = arr3[1];
arr3[1] = arr3[0];
arr3[0] = temp;
console.log( arr3 ); // [6, 3, 4, 2, 11, 10, 5]
*/

/*
过程：

比较相邻的元素。如果第一个比第二个大，就交换他们两个。
对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对。这步做完后，最后的元素会是最大的数。【关键】
针对所有的元素重复以上的步骤，除了最后一个。


一共5轮，每一轮遍历5个元素, 如[3,5,2,1,4] 

第一轮：
       ┌┈┈┐
       3 < 5  2  1  4
             ↓
          ┌┈┈┐
       3  5 > 2  1  4
             ↓
              ┌┈┈┐
       3  2  5 > 1  4
             ↓
                ┌┈┈┐
       3  2  1  5 > 4
             ↓
结果:  3  2  1  4  5

--------
第二轮：
       ┌┈┈┐
       3 > 2  1  4  5
             ↓
          ┌┈┈┐
       2  3 > 1  4  5
             ↓
              ┌┈┈┐
       2  1  3 < 4  5
             ↓
                ┌┈┈┐
       2  1  3 < 4  5   (如果使用j<len - 1 - i，那么将跳过此步)
             ↓
结果:  2  1  3  4  5

--------
第三轮：
       ┌┈┈┐
       2 > 1  3  4  5
             ↓
          ┌┈┈┐
       1  2 < 3  4  5
             ↓
              ┌┈┈┐
       1  2  3 < 4  5    (如果使用j<len - 1 - i，那么将跳过此步)
             ↓
                ┌┈┈┐
       1  2  3  4 < 5   (如果使用j<len - 1 - i，那么将跳过此步)
             ↓
结果:  1  2  3  4  5

--------
第四轮和第五轮都是 1  2  3  4  5

*/

let arr = [3,5,2,1,4];
const len = arr.length;

//第一层循环（轮数）
for (let i=0;i<len;i++ ) {
    //第二层循环（比较相邻）
    for (let j=0;j<len - 1;j++) { // len - 1 可以写成 len - 1 - i 提高性能

        const itemPrev = arr[j];
        const itemNext = arr[j+1];
        let middleItem = itemNext; //中间件

        if ( itemPrev > itemNext ) {  // 相邻元素两两对比(注意索引是j)
            //调换位置 (可以使用E6的解构写法：[arr[j], arr[j+1]] = [arr[j+1], arr[j]] )
            arr[j] = middleItem;
            arr[j+1] = itemPrev;
        }
    }

}
console.log( arr );  // [1, 2, 3, 4, 5]

//如果要降序排列，则把 `itemPrev > itemNext` 改成 `itemPrev < itemNext` 即可

```
**-->时间复杂度为O(n^2)**

### 选择排序
----

```js


/*
过程：

相当于 "原址比较"

找到数据结构中的最小值，将其放在第一位；
接着找第二小的值，放在第二位；
以此类推。

一共4轮，每一轮遍历5个元素, 如[3,5,2,1,4] 

第一轮：min: arr[0] = 3
             ↓
       ┌┈┐
       3  5  2  1  4  ---> min: 3
             ↓
       ┌┈┈┈┈┐
       3  5  2  1  4  ---> min: 2 (交换)
             ↓
       ┌┈┈┈┈┈┈┈┐
       2  5  3  1  4  ---> min: 1 (交换)
             ↓
       ┌┈┈┈┈┈┈┈┈┈┈┐
       1  5  3  2  4  ---> min: 1

--------
第二轮：min: arr[1] = 5
             ↓
          ┌┈┐
       1  5  3  2  4  ---> min: 3 (交换)
             ↓
          ┌┈┈┈┈┈┐
       1  3  5  2  4  ---> min: 2 (交换)
             ↓
          ┌┈┈┈┈┈┈┈┈┐
       1  2  5  3  4  ---> min: 2

--------
第三轮：min: arr[2] = 5
             ↓
             ┌┈┈┐
       1  2  5  3  4  ---> min: 3 (交换)
             ↓
             ┌┈┈┈┈┈┐
       1  2  3  5  4  ---> min: 3

--------
第四轮：min: arr[3] = 5
             ↓
                ┌┈┈┐
       1  2  3  5  4  ---> min: 4 (交换)
             ↓
                ┌┈┈┈┈┈┐
       1  2  3  4  5   x  ---> (结束，结果）


*/

let arr = [3,5,2,1,4];
const { length } = arr;
let indexMin; //临时最小值，不断改变(索引)

for (let i=0;i<length - 1;i++ ) {  //一共4轮
    indexMin = i;  

    for (let j=i;j<length;j++) { // 遍历所有5个元素
        if ( arr[indexMin] > arr[j] ) { 
            indexMin = j;
        }
    }

    //调换位置
    if (i !== indexMin) {
        [arr[i], arr[indexMin]] = [arr[indexMin], arr[i]];
    }


}
console.log( arr );  // [1, 2, 3, 4, 5]

```

解法2：不使用临时索引
```js
let arr = [3,5,2,1,4];
const { length } = arr;

for (let i=0;i<length;i++ ) {  //一共5轮
    //每轮比较的次数
    for (let j=i+1;j<length;j++) {  //注意： j=i+1, 比较的是i和j
        if ( arr[i] > arr[j] ) { 
            [arr[i], arr[j]] = [arr[j], arr[i]];
        }
    }
}
console.log( arr );  // [1, 2, 3, 4, 5]
```

**-->时间复杂度为O(n^2)**


### 插入排序
----

类似扑克牌。相当于分为2个表，有序表和无序表。每次以无序表中取出一个元素，插入到有序表中，第一个元素默认为有序；把后面的元素逐个插入到前面的有序队列中。

**关键：内循环是"反着"比较（相邻对比排序）**

例： [5，3，2，1，4]

**过程为：**
```base
第一轮：

有序：5
无序：3，2，1，4

第二轮：

有序：3，5 (已排序)
无序：2，1，4


第三轮：

有序：1，3，5 (已排序)
无序：1，4

第四轮：

有序：1，2，3，5 (已排序)
无序：4

第五轮：

有序：1，2，3，4，5 (已排序)
无序：null
```

```js
/*
过程：

比较相邻的元素。如果第一个比第二个大，就交换他们两个。
对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对。这步做完后，最后的元素会是最大的数。【关键】
针对所有的元素重复以上的步骤，除了最后一个。


一共4轮，每一轮反向递减遍历，原数据 [5,3,1,4,2] 

第一轮： (循环1次) curIndex = 1; 开始值: 3
       
       ┌┈┈┈┐
       5 > 3  1  4  2 , curIndex = 0 
         
             ↓
结果:  3  5  1  4  2

--------
第二轮： (循环2次) curIndex = 2; 开始值: 1
       
          ┌┈┈┈┐
       3  5 > 1  4  2  , curIndex = 1 
             ↓
       ┌┈┈┈┐
       3 > 1  5  4  2 , curIndex = 0
           
             ↓
结果:  1  3  5  4  2

--------
第三轮： (循环3次) curIndex = 3; 开始值: 4

             ┌┈┈┈┐
       1  3  5 > 4  2 , curIndex = 2
             ↓
          ┌┈┈┈┐
       1  3   4  5  2 , curIndex = 1
             ↓
       ┌┈┈┈┐
       1   3  4  5  2 , curIndex = 0
       
             ↓
结果:  1  3  4  5  2

--------
第四轮： (循环4次) curIndex = 4; 开始值: 2

                ┌┈┈┈┐
       1  3  4  5 > 2 , curIndex = 3
             ↓
             ┌┈┈┈┐
       1  3  4 > 2  5 , curIndex = 2
             ↓
          ┌┈┈┈┐
       1  3 > 2  4  5 , curIndex = 1
             ↓
       ┌┈┈┈┐
       1   2  3  4  5 , curIndex = 0
        
             ↓
结果:  1  2  3  4  5


*/

```

方法1：内层使用for循环
```js
function insertionSort( array ) {
    const {length} = array;
    let curIndex;
    for (let i = 1; i < length; i++) { //从1开始，第一个已经默认算排序
        curIndex = i;

        /*
        这里的for也可写成：
        let j = i;
        while (j>=0) {
            if ( array[j] > array[curIndex]) { ... }
            j--;
        }
        */
        for (let j = i - 1; j >= 0; j--) {
            if ( array[j] > array[curIndex]) {
                const temp = array[curIndex];
                array[curIndex] = array[j];
                array[j] = temp;

                //当前索引往前推1
                curIndex = j;

            }
        }

    }

    return array;
}

console.log( insertionSort([5,3,2,1,4]) );  // [1, 2, 3, 4, 5]
```

方法2：内层用while
```js
function insertionSort( array ) {
    const {length} = array;
    let temp;
    for (let i = 1; i < length; i++) { //从1开始，第一个已经默认算排序
        let j = i;
        temp = array[i];

        while (j > 0 && array[j-1] > temp) {
            array[j] = array[j-1];
            j--;
        }

        //如果 array[j-1] < temp，则跳出while循环，使用 j-- 后的新的索引j，替换成temp，以便实现元素的交换
        array[j] = temp;

    }

    return array;
}

```

**-->时间复杂度为O(n^2)**

### 希尔排序
----

**插入排序改进版**。它与插入排序的不同之处在于，它会优先比较距离较远的元素。希尔排序又叫缩小增量排序。

1. 把记录按下标的一定增量分组，对每组使用直接插入排序算法排序；
2. 随着增量逐渐减少，每组包含的元素越来越多，当增量减至1时，整个文件恰被分成一组，算法便终止。
3. 希尔算法进行到最后，增量或者称跳跃数变为1的时候，它就是直接插入排序，只不过这时候，整个序列基本上是有序的，需要交换的数据已经非常少了，提高效率。


#### 算法步骤 
```js
/*
过程：

原数据 [5,3,1,4,2] , 增量将产生2次外循环



第一次外循环：初始增量：5/2 下舍后等于2; 
--------------------------------

比较过程如下：

第一轮： (循环1次) curIndex = 2; 开始值: 1
       
       ┌┈┈┈┈┈┈┈┐
       5   3    1    4    2 , curIndex = 0, 因为 5 > 1， 交换它们
         
             ↓
结果:  1  3  5  4  2
 
--------
第二轮： (循环2次) curIndex = 3; 开始值: 4
       
           ┌┈┈┈┈┈┈┈┈┐
       1   3    5    4    2  , curIndex = 1
             ↓
             
      curIndex = -1 停止
           
             ↓
结果:  1  3  5  4  2

--------
第三轮： (循环3次) curIndex = 4; 开始值: 2

                ┌┈┈┈┈┈┈┈┈┐
       1   3    5    4    2  , curIndex = 2, 因为 5 > 2， 交换它们
             ↓
       ┌┈┈┈┈┈┈┈┈┐
       1   3    2    4    5  , curIndex = 0

             ↓
结果:  1  3  2  4  5





第二次外循环：增量缩小为1，排序 1  3  2  4  5 后得到最终排序
--------------------------------

比较过程如下：
      (... 省略)
结果:  1  2  3  4  5


*/
```

#### 解法：
```js
function shellSort(arr) {
    let len = arr.length;

    //初始化增量(间隔)
    let gap = Math.floor(len / 2);

    while (gap >= 1) {
        //gap分组，分组进行排序
        for (let i = gap; i < len; i++) {
            let j = i;
            let temp = arr[i];
        
            while (j - gap >= 0 && arr[j - gap] > temp ) {
                arr[j] = arr[j - gap];
                j = j - gap;
            }

            //如果 arr[j - gap] < temp，则跳出while循环，使用 j-- 后的新的索引j，替换成temp，以便实现元素的交换
            arr[j] = temp;

        }

        gap = Math.floor(gap / 2);
    }
    
    /*
    也可以使用for写法：
    for (let gap = Math.floor(len / 2); gap > 0; gap = Math.floor(gap/2)){ ... }
    */


    return arr;

}

console.log( shellSort([5,3,1,4,2]) );  // [1, 2, 3, 4, 5]
```


在一般情况下，while 循环将在中间退出

即 1/2 + 2/2 + 3/2 + 4/2 + .... + (n-1)/2 = Theta((n^2)/2) = Theta(n^2) = Ω(n^2)

这里实现了 (n^2)/2，即使除以 2 并没有更大的区别。希尔排序只不过是通过使用 n/2、n/4、n/8、....、2、1 之类的间隙进行插入排序，这意味着它利用了插入排序的最佳情况复杂性（即 while 循环退出）。
一旦我们在插入元素左侧找到小元素，就会很快，因此它加起来就是总执行时间。

n/2 + n/4 + n/8 + n/16 + .... + n/n = n * (1/2 + 1/4 + 1/8 + 1/16 + ... + 1/n) = n * log(n) 

**-->时间复杂度：O(n*log(n))**

由于希尔排序是原地排序算法，空间复杂度为常数阶 O(1)



### 数组去重
----

#### 使用indexOf()方法去重

```js
function unique(arr) {
    if ( Object.prototype.toString.call( arr ) !== '[object Array]' ) return;

    const res = [];
    arr.forEach( function(item, index, arr) {
        if (res.indexOf(item) === -1 ) {
            res.push(item);
        }
    })

    return res;
}

console.log( unique([2,3,1,2,3,1,5,6,'a','b','b']) ); // [2, 3, 1, 5, 6, 'a', 'b']
```

#### 使用splice()方法去重

```js
/*
【注意：splice后的数组，会覆盖原来的数组，造成for或者forEach循环的次数减少】
过程：

原数据：2,5,3,6,2,4,5,8,3
第一次索引对比：2的第一个和最后一个索引为：0 | 4，索引存在元素，证明有重复元素，则删除其中一个，变为 5,3,6,2,4,5,8,3
第二次索引对比：5的第一个和最后一个索引为：0 | 5 【因为删除了一个2】，索引存在元素，证明有重复元素，则删除其中一个，变为 3,6,2,4,5,8,3
第三次索引对比：3的第一个和最后一个索引为：0 | 6 【因为删除了一个2，一个5】，索引存在元素，证明有重复元素，则删除其中一个，变为 6,2,4,5,8,3
第四次索引对比：6的第一个和最后一个索引为：0 | -1 【因为删除了一个2，一个5，一个3】，索引只有1个存在，证明无重复元素，变为 6,2,4,5,8,3
第五次索引对比：4的第一个和最后一个索引为：2 | -1 【因为删除了一个2，一个5，一个3】，索引只有1个存在，证明无重复元素，变为 6,2,4,5,8,3
第六次索引对比：8的第一个和最后一个索引为：4 | -1 【因为删除了一个2，一个5，一个3】，索引只有1个存在，证明无重复元素，变为 6,2,4,5,8,3

*/

function unique(arr) {
    // lastIndexOf() 方法返回调用String 对象的指定值最后一次出现的索引，
    //在一个字符串中的指定位置 fromIndex处从后向前搜索。如果没找到这个特定值则返回-1 。

    arr.forEach(function (el, i, oarr) {

        console.log( i ); // 输出：0, 1, 2, 3, 4, 5
        console.log('oarr: ', oarr); // oarr 的数量改变：9 -> 8 -> 7 -> 7 -> 7 -> 6
        
        if (arr.indexOf(el) !== arr.lastIndexOf(el)) {
            arr.splice(i, 1);
        }

    });

    return arr;
}


var arr = [2,5,3,6,2,4,5,8,3];
console.log(unique(arr))  // [5, 6, 2, 4, 8, 3]
	

//----------

function unique(arr) {

    arr.forEach(function (el, i, oarr) {

        console.log('oarr: ', oarr); // oarr 的数量改变：9 -> 8 -> 7 -> 7 -> 7 -> 6
        if (arr.indexOf(el) !== arr.lastIndexOf(el)) {
            arr.splice(arr.indexOf(el), 1); //如果将 i 改为 arr.indexOf(el)，结果就和过程分析的一致
        }

    });

    return arr;
}


var arr = [2,5,3,6,2,4,5,8,3];
console.log(unique(arr))  // [6, 2, 4, 5, 8, 3]

```
```js
/*
【注意：splice后的数组，会覆盖原来的数组，造成for或者forEach循环的次数减少】
过程：
itemFirst从外层循环中取元素，itemSecond是线条括起来的那些元素，一共进行了6次外循环

初始状态：2, 5, 3, 6, 2, 4, 5, 8, 3

第一次大循环（i=0,j=1）

   ┌┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┐
2, 5, 3, 6, 2, 4, 5, 8, 3
-----------
2 ==  5
2 ==  3
2 ==  6
2 ==  2 (删除，同时新的数组立刻变为：2, 5, 3, 6, 4, 5, 8, 3)
2 ==  4
2 ==  5
2 ==  8
2 ==  3


第二次大循环（原数组数量减少1） （i=1,j=2）

      ┌┈┈┈┈┈┈┈┈┈┈┈┈┈┐
2, 5, 3, 6, 4, 5, 8, 3
-----------
5 ==  3
5 ==  6
5 ==  4
5 ==  5 (删除，同时新的数组立刻变为：2, 5, 3, 6, 4, 8, 3)
5 ==  8
5 ==  3

第三次大循环（原数组数量减少2） （i=2,j=3）

         ┌┈┈┈┈┈┈┈┈┐
2, 5, 3, 6, 4, 8, 3
-----------
3 ==  6
3 ==  4
3 ==  8
3 ==  3(删除，同时新的数组立刻变为：2, 5, 3, 6, 4, 8)

第四次大循环（原数组数量减少3） （i=3,j=4）

            ┌┈┈┈┐
2, 5, 3, 6, 4, 8
-----------
6 ==  4
6 ==  8

第五次大循环（原数组数量等于6，保持不变） （i=4,j=5）

               ┌┈┈┐
2, 5, 3, 6, 4, 8 null
-----------
4 ==  8

第六次大循环（原数组数量等于6，保持不变） （i=5,j=6）
-----------
结束

*/



function unique(arr) {
    for (let i = 0; i < arr.length; i++) {
        for (let j = i + 1; j < arr.length; j++) { // 特别注意：往后推的元素，要使用i+1
        
            const itemFirst = arr[i];
            const itemSecond = arr[j];

             //第一个等同于第二个，splice方法删除第二个
            if (itemFirst === itemSecond) { 
                arr.splice(j, 1); //删除 j 这个索引位置
            }
        }
    }
    return arr;
}

let arr = [2,5,3,6,2,4,5,8,3];
console.log(unique(arr)); // [2, 5, 3, 6, 4, 8]
    
```
```js

//注意：纯字符串可能出现未完全去重，需要再次过滤, 一般来说使用数字数组来去重【直接使用字符串去重不是最佳体验】

const arr = [1,4,3,5,6,7,3,2,7,8,2,null,undefined,8,1,undefined,"a","b","a"];
arr.sort(function(a,b){
  if (a < b) {
    return -1;  // 小于 0 ，那么 a 会被排列到 b 之前；
  }
  if (a > b) {
    return 1;
  }

  return 0;
});	//先排序，然后才能比较第一个等同于第二个，splice方法删除第二个

function unique( cusArr ) {
	for (let i = 0; i < cusArr.length; i++ ) {

		if ( cusArr[i] === cusArr[i+1]) {
			cusArr.splice(i+1,1);
		}
	}	
};
unique( arr );
	
console.log(arr); //[null, 1, 2, 3, 4, 5, 6, 7, 8, 'a', 'b', undefined]
	
```

#### 利用reduce+includes
```js
function unique(arr){
    return arr.reduce((prev,cur) => prev.includes(cur) ? prev : [...prev,cur],[]);
}
var arr = [1,1,'true','true',true,true,15,15,false,false, undefined,undefined, null,null, NaN, NaN,'NaN', 0, 0, 'a', 'a',{},{}];
console.log(unique(arr));
// [1, "true", true, 15, false, undefined, null, NaN, "NaN", 0, "a", {…}, {…}]
```


#### 利用hasOwnProperty和filter去重

```js
var arr = [1,2,4,4,6,'h','fg','h'];
var obj = {};
var arr2 = arr.filter((item,index) => {

	// 因为object的key是string类型的，无论你传入什么，他都会解析成字符串(symbol和Map等新特性除外)，所以这就导致了数字1和字符串1，他在object中的key是相同的，而你应该不想把他们当成相同的数据给去除掉。
	//所以这里使用了typeof来简单判断了下类型。
	//判断obj是否包含重复的key值
	const _key = `${typeof item}_${item}`; 

	if ( !obj.hasOwnProperty(_key) ) {
		obj[_key] = item;
		return true;
	} else {
		return false;
	}
	
});
	
console.log( Object.keys(obj) ); // ['number_1', 'number_2', 'number_4', 'number_6', 'string_h', 'string_fg']
console.log( arr2 ); // [1, 2, 4, 6, 'h', 'fg']

```

换一种封装方法：

```js
Array.prototype.unique = function () {
	var arr = this;
	var obj = {};
	var arr2 = arr.filter((item,index) => {

		// 因为object的key是string类型的，无论你传入什么，他都会解析成字符串(symbol和Map等新特性除外)，所以这就导致了数字1和字符串1，他在object中的key是相同的，而你应该不想把他们当成相同的数据给去除掉。
		//所以这里使用了typeof来简单判断了下类型。
		//判断obj是否包含重复的key值
		const _key = `${typeof item}_${item}`; 

		if ( !obj.hasOwnProperty(_key) ) {
			obj[_key] = item;
			return true;
		} else {
			return false;
		}

	});
	
	return arr2;
}
	
var arr = [1,2,4,4,6,'h','fg','h'];
console.log( arr.unique() ); // [1, 2, 4, 6, 'h', 'fg']

```


### 判断质数
----

优化时间复杂度，避免过大的数字timeout

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
```js
const isPrime = num => {
  for (let i = 2; i <= num ** .5; i++) {
    if (!(num % i)) return false;
  }
  return num > 1;
}

```

### 缓动算法-趋近10 
----
一个缓动无限趋近于 10 (目标值)的算法

```js
let increase = 0;
let anime = null;
const ease = 0.15;

render();
function render() {
	anime = requestAnimationFrame( render );
	increase += ( 10 - increase ) * ease;
	console.log( 'increase: ', increase );

	//停止动画
	if ( increase === 9.999999999999995 ) window.cancelAnimationFrame( anime );
}

//结果，无限趋近于 10
// 2.775
// 3.8587499999999997
// 4.7799375
// 5.562946875
// 6.22850484375
// 6.794229117187499
// 7.275094749609375
// 7.683830537167968
// 8.031255956592773
// 8.326567563103858
// ...
// 9.999999999999995

```

```bash
【思考与演算原理】

increase1 = ( 10 - 0 ) * ease = 1.5;
increase2 = ( 10 - 1.5 ) * ease = 1.275;
increase3 = ( 10 - 1.5 - 1.275 ) * ease = 1.08375;
...
increase999 = ( 10 - 1.5 - 1.275 - ... ) * ease = 0.075;

increaseAll = increase1 + increase2 + increase3 + ... + increase999 ≈ 10

结果趋近于 10
```


### 缓动算法-趋近0
----
一个缓动无限趋近于 0 (目标值)的算法

```js
let increase = 0;
let anime = null;
const ease = 0.15;

render();
function render() {
	anime = requestAnimationFrame( render );
	increase += ( 10 - increase ) * ease;
	
	//科学计数法需要转数字
	const res = parseFloat( 10 - increase ).toFixed(7);
	console.log( 'res: ', res );

	//停止动画
	if ( increase === 9.999999999999995 ) window.cancelAnimationFrame( anime );
}

```

```bash
【思考与演算原理-step1】

increase = ( 10 - 0 ) * ease = 1.5;
increase = ( 10 - 1.5 ) * ease = 1.275;
increase = ( 10 - 1.5 - 1.275 ) * ease = 1.08375;
...
increase = ( 10 - 1.5 - 1.275 - ... ) * ease = 0.075;

结果趋近于0 


【思考与演算原理-step2】

increase1 = ( 10 - 0 ) * ease = 1.5;
increase2 = ( 10 - 1.5 ) * ease = 1.275;
increase3 = ( 10 - 1.5 - 1.275 ) * ease = 1.08375;
...
increase999 = ( 10 - 1.5 - 1.275 - ... ) * ease = 0.075;


result = 10 - increase1 = 8.5000000;
result = 10 - increase1 - increase2 = 7.2250000;
result = 10 - increase1 - increase2 - increase3 = 6.1412500;
result = 10 - increase1 - increase2 - increase3 - ... - increase999 ≈ 0.0000000


//结果，无限趋近于 0
8.5000000
7.2250000
6.1412500
5.2200625
4.4370531
3.7714952
...
0.0000001
0.0000000
```

### 计算坐标的弧度角度
----

根据弧度定义，一周的弧度数为2πr/r=2π，360°角=2π弧度，因此，1弧度约为57.3°，即57°17'44.806''，1°为π/180弧度，近似值为0.01745弧度，周角为2π弧度，平角（即180°角）为π弧度，直角为π/2弧度。 在具体计算中，角度以弧度给出时，通常不写弧度单位，直接写值。

只要记住：圆的周长是2πr，180°的弧度是π


```js
/*
* Returns the degree from radian.
*
* @return {Number} rad - Value of radian.
* @return {Number}
* @usage: 

angle = rad / ( Math.PI / 180 )  = rad * ( 180/Math.PI );
*/
function getDegree(rad) {
	return rad / Math.PI * 180;
}


/*
* Returns the radian degree .
*
* @return {Number} deg - Value of degree.
* @return {Number}
* @usage: 

rad = Math.PI / 180 * 30 ;
*/
function getRadian(deg) {
	return deg * Math.PI / 180;
}

//---------------------

console.log( 'Math.PI: ', Math.PI );  // 3.141592653589793
console.log( 'degree: ', getDegree(1.5) );  // 85.94366926962348
console.log( 'degree: ', getDegree(2*3.1415926) );  // 359.9999938590621  (2π = 6.283052)

```

**根据实际的坐标计算弧度和角度**

Math.atan2() 返回从原点(0,0)到(x,y)点的线段与x轴正方向之间的平面角度(弧度值)，也就是Math.atan2(y,x)

atan2 方法返回一个 -pi 到 pi 之间的数值，表示点 (x, y) 对应的偏移角度。这是一个逆时针角度，以弧度为单位，正X轴和点 (x, y) 与原点连线 之间。注意此函数接受的参数：先传递 y 坐标，然后是 x 坐标。


```js
const radians = Math.atan2(15, 15);
const degrees = getDegree(radians);

console.log( 'radians', radians );  // 0.7853981633974483
console.log( 'degrees', degrees );  // 45
```






### 斐波那契数列
----
给定一个数 N 返回斐波那契数列的索引值。


#### 概念

斐波那契数列（Fibonacci sequence），又称黄金分割数列，因数学家莱昂纳多·斐波那契（Leonardo Fibonacci）以兔子繁殖为例子而引入，故又称为“兔子数列”，指的是这样一个数列：1、1、2、3、5、8、13、21、34、……

在现代物理、准晶体结构、化学等领域，斐波纳契数列都有直接的应用。

递推公式：F(n) = F(n-1) + F(n-2)


#### 运用场景

 - 摆砖头
 - 创作旋律

```base

 砖头数列：
 
 n = 0   -->  1种
 
 n = 1   -->  ▋  1种
 
 n = 2   -->  ▋▋   〓 2种 
 
 n = 3   -->  ▋▋▋    ▋〓     〓 ▋ 3种 

```


#### 解法
```js
//迭代
//----------
function fibonacci(num) {
    let a = 1, b = 0, temp;
    
    while (num >= 0) {

        console.log(temp); // undefined, 1, 1, 2, 3, 5

        temp = a;
        a = a + b;
        b = temp;
        num--;
        
    }


    return b;
}

console.log( fibonacci(5) ); // 8


//递归
//----------
function fibonacci(num) {
  if (num <= 1) return 1;

  return fibonacci(num - 1) + fibonacci(num - 2);
}

console.log( fibonacci(5) ); // 8
```








### 翻转(反转)单链表
----

反转单链表。(链表的开头称为头，链表末尾的节点称为尾)

例子:

 - Input: 1->2->3->4->5->NULL
 - Output: 5->4->3->2->1->NULL


#### 数据结构
```base
 Head(头)
 ↓
[A:next]  ->   [B:next]  ->   [C:next]  ->   NULL
 ↑  ↑
 值 指针
```



#### 过程 (注意：这其实是类似一个"传递性"的问题)
------------------------------------------

传递性关系结构： 若 B = CD, 则 AB = ACD

```base

[node(A):next(B)]     ->    [node(B):next(C)]      ->    [node(C):next(NULL)]

 
 - 将 tmp 存储为旧的next节点， 当前节点的 next 节点设置为 previous
 - while循环将按次序传入 node(A), node(B), node(C)
 - ▲ 表示相对应


第一步：变量值

previous     ->     previous     ->     previous 
   ↑                  ↑                    ↑       
  NULL               node(A)             node(B)
  
  
  
  tmp     ->         tmp     ->           tmp 
   ↑                  ↑                    ↑       
  node(B)           node(C)               NULL
  

第二步：设置传入的节点的 next 为 previous


[node(A):next(NULL)]     ->    [node(B):next(A)]      ->   [node(C):next(B)]



第三步：previous 重新赋值为当前 node， 完成和原节点的调换


previous         ->            previous          ->        previous 
   ↑                              ↑                           ↑       
[node(A):next(NULL)]     ->    [node(B):next(A)]      ->    [node(C):next(B)]
                                                                    ▲


第四步：当前 node 重新赋值为 tmp


[node(B):next(A)]     ->    [node(C):next(B)]      ->   NULL
                                   ▲


第五步：由于 node(B)的next是A, 所以 C 节点

[node(C):next(B)] 相当于  [node(C):next([node(B):next(A)])]

所以最终结果返回第三步的最后一个 previous 的值即可

```



#### 解法
```js

// 单向链表(Single Linked List)
class LinkedListNode {
    constructor(value) {
        this.value = value;
        this.next = null;
    }
}
/*
也可以写成

function LinkedListNode(value) {
    this.value = value;
    this.next = null;
}
*/


//迭代法 => 时间复杂度O(n)  空间复杂度 O(1)
//------------------------
function reverseLinkedList(head) {
    let node = head,
        previous = null,  // 前一个节点指针,默认设置一个null值，否则反转后最后的结果会缺少next的null值
        tmp;

    while (node) {
        // 我们遍历列表一次，将每个节点的下一个指针更改为前一个节点
        // 将 node.next 复制到 tmp 中，然后再将 node.next 设置为上一个
        tmp = node.next;

        // 反向指针
        node.next = previous;

        // 在列表中前进
        previous = node;

        // 基线条件(当node为null时跳出while循环)
        node = tmp;
    }

    return previous;
}



//递归 => 时间复杂度O(n)  空间复杂度 O(n)
//------------------------
function reverseLinkedList(head) {
    if (!head || !head.next) {
        return head;
    }
    let tmp = reverseLinkedList(head.next);
    head.next.next = head;
    head.next = null;
    return tmp;
}


const linkedList = new LinkedListNode(1);
linkedList.next = new LinkedListNode(2);
linkedList.next.next = new LinkedListNode(3);
linkedList.next.next.next = new LinkedListNode(4);
console.log( JSON.stringify(linkedList) );
/*输出：
{
  data: 1,
  next: {
    data: 2,
    next: {
      data: 3,
      next: {
        data: 4,
        next: null
      }
    }
  }
}
*/

console.log( JSON.stringify(reverseLinkedList(linkedList)) );
/*输出：
{
  data: 4,
  next: {
    data: 3,
    next: {
      data: 2,
      next: {
        data: 1,
        next: null
      }
    }
  }
}
*/
```




### 设计LRU
----

设计一个遵循最近最少使用 (LRU) 缓存约束的数据结构。

LRU是Least Recently Used的缩写，即最近最少使用，是一种常用的页面置换算法，选择最近最久未使用的页面予以淘汰。


#### 数据结构
```base
哈希表(Hash Table)

 Key  | Value
--------------
  1   |  A
  2   |  B
  3   |  C
  4   |  
  5   |   
  6   |   
  7   |   
  8   |   
  9   |   


双向链表(Double Linked List)

 null A  B              A  B  C             B  C null
   ↓  ↓  ↓              ↓  ↓  ↓              ↓  ↓  ↓
[prev:1:next]  <->   [prev:2:next]  <->   [prev:3:next]

```

 
 
#### 解法1(使用哈希表)
```js
class LRU {
    constructor(max = 10) {
        this.max = max;
        this.cache = new Map();
    }

    get(key) {
        let item = this.cache.get(key);
        if (item) {
            // 刷新键
            // 相当于把用过的元素往后移
            // 即使用cache.get("red")，则 {'red' => 'red', 'grey' => 'grey'} 变成 {'grey' => 'grey', 'red' => 'red'}
            this.cache.delete(key);
            this.cache.set(key, item);
        }
        return item;
    }

    set(key, val) {
        // 刷新键
        if (this.cache.has(key)) this.cache.delete(key);

        // 驱逐最旧的元素  (最近，最少使用的grey将被删除)
        else if (this.cache.size == this.max) this.cache.delete(this.first());
        this.cache.set(key, val);
    }

    first() {
        // 也可以写成 Array.from( this.cache.keys() )[0]
        return this.cache.keys().next().value; // 内置遍历器
    }
}



// 注意缓存cache输出的顺序，和双链表的有区别

const cache = new LRU(2); // 缓存容量为2
cache.set("red", "red"); //缓存 {red=red}
cache.set("grey", "grey"); //缓存 {red=red, grey=grey}
//----

const param_1 = cache.get("red");
console.log(param_1); // red (从缓存中获取)
//----

cache.set("yellow", "yellow"); // 添加新键，超过了容量，则grey将被淘汰，缓存有 {red=red, yellow=yellow}
//----

const param_2 = cache.get("grey");
console.log(param_2); // undefined
//----


```



#### 解法2-1(使用双链表)

【特别注意节点的交换时，左右"指针"的变化的"当前节点的值"的变化】

在双向链表中，将 head 设为最近使用的并将 tail 设为最近最少使用的。

 - 在头部进行每次插入。
 - 在每次读取或更新操作时，将节点与其位置分离，并将其附加到 LinkedList 的头部。 
 - 当缓存限制超过时，从尾部删除一个节点 (因为每次使用的都往前移，所以结尾的元素是最少使用的)
 - 时间复杂度 O(1)

```js
class Node {
    constructor(key, value, next = null, prev = null) {
        this.key = key;
        this.value = value;
        this.next = next;
        this.prev = prev;
    }
}

class LRU {
    constructor(limit = 10) {
        this.size = 0;
        this.limit = limit;
        this.head = null;
        this.tail = null;
        this.cacheMap = {};
    }

    set(key, value) {
        const existingNode = this.cacheMap[key];
        if (existingNode) {
            this.detach(existingNode);
            this.size--;
        } else if (this.size === this.limit) {
            delete this.cacheMap[this.tail.key];
            this.detach(this.tail);
            this.size--;
        }

        // 写入 LinkedList 的头部
        if (!this.head) {
            this.head = this.tail = new Node(key, value);
        } else {
            const node = new Node(key, value, this.head);
            this.head.prev = node;
            this.head = node;
        }

        // 使用链表键和节点引用更新缓存映射
        this.cacheMap[key] = this.head;
        this.size++;
    }

    get(key) {
        const existingNode = this.cacheMap[key];
        if (existingNode) {
            const value = existingNode.value;
            // 如果尚未将该节点设为 LinkedList 的新 Head
            if (this.head !== existingNode) {
                // 自动从它的位置删除节点并使其成为一个新的头，即最常用的
                this.set(key, value);
            }
            return value;
        }

        return undefined;
    }


    //分离
    detach(node) {
        if (node.prev !== null) {
            node.prev.next = node.next;
        } else {
            this.head = node.next;
        }

        if (node.next !== null) {
            node.next.prev = node.prev;
        } else {
            this.tail = node.prev;
        }
    }

}
```


#### 解法2-2(使用双链表, 详细分解注释，便于理解)

```js
class Node {
    constructor(key, value, next = null, prev = null) {
        this.key = key;
        this.value = value;
        this.next = next;
        this.prev = prev;
    }
}

class LRU {
    constructor(limit = 10) {
        this.size = 0;
        this.limit = limit;
        this.head = null;
        this.tail = null;
        this.cache = {};
    }

    set(key, value) {
        let newNode;

        if ( this.cache[key] === undefined )  newNode = new Node(key, value);


        // 链表为空
        //--------------
        if (this.size === 0) {
            this.head = newNode;
            this.tail = newNode;
            this.size++;
            this.cache[key] = newNode;
            return this;
        }


        // 链表容量超限
        //--------------
        if (this.size === this.limit) {
            //删除节点
            delete this.cache[this.tail.key]

            //设置新的尾部节点为它的前一个节点
            this.tail = this.tail.prev;
            this.tail.next = null;
            this.size--;
        }


        // 节点添加到头部（step1~5）
        //--------------
        // step1: 头部的前一个节点指针设置为当前节点
        this.head.prev = newNode;

        // step2: 当前节点的下一个节点指针设置成之前的头部节点
        newNode.next = this.head;  

        // step3: 头部节点替换成当前节点
        this.head = newNode;

        // step4: 当前节点的前一个节点指针设置为null
        newNode.prev = null;

        // step5: 计数+1
        this.size++;

        /*
        
        第一步(原链表):
        
            [prev:1:next]       ->        [prev:2:next]
                  ↑
                 head 
        
        
        
        第二步(新节点和原head的互换):
        
             ┌┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┐
           [prev:1:next]            [prev:3:next]            ->       [prev:2:next]
                 ↑                        ↑
               原head                 newNode(尚未加入链表) 
         
         
               
        第三步(形成新的链表,多个一个元素到链中，节点1的next指针没有改变):  
               
            null   节点1                  节点3    节点2
             ↓       ↓                      ↓       ↓
           [prev:3:next]       ->         [prev:1:next]       ->        [prev:2:next]
                 ↑                              ↑
                新head(newNode)               原head
        
        */


        
        //添加到缓存
        //--------------
        this.cache[key] = newNode;
        return this;

    }


    get(key) {
        if (!this.cache[key]) {
            return undefined;
        }

        let foundNode = this.cache[key];
        let foundNodePrev = foundNode.prev; // 当前节点的前一个节点
        let foundNodeNext = foundNode.next;  // 当前节点的后一个节点

        // 如果当前节点是头 (直接返回节点，因为不需要任何位置交换)
        //--------------
        if (foundNode === this.head) {
            return foundNode.value;
        }

        // 如果当前节点是尾 (它后面没有元素，无需考虑foundNodeNext，所以它只和前一个节点交换位置)
        //--------------
        if (foundNode === this.tail) {
            if (foundNodePrev) foundNodePrev.next = null;  // 前一个节点的下一个指针变成null
            this.tail = foundNodePrev; // 尾部节点替换成前一个节点 (当前节点依然存在，然后下一步时当前节点会作为"新节点"移动到头部位置)
        }

        // 如果当前节点居于头尾之间 (要将当前节点的prev和next指针拆开，分配到当前节点的前后节点指针中, 然后下一步时当前节点会作为"新节点"移动到头部位置)
        //--------------
        if (foundNode !== this.head && foundNode !== this.tail) {
            if (foundNodePrev) foundNodePrev.next = foundNodeNext;  // 前一个节点的下一个指针变成当前节点的后一个节点
            if (foundNodeNext) foundNodeNext.prev = foundNodePrev;  // 后一个节点的上一个指针变成当前节点的前一个节点
        }

        
        // 节点添加到头部（step1~4, 元素size没有改变）
        //--------------
        // step1: 头部的前一个节点指针设置为当前节点
        this.head.prev = foundNode;

        // step2: 当前节点的下一个节点指针设置成之前的头部节点
        foundNode.next = this.head;  

        // step3: 头部节点替换成当前节点
        this.head = foundNode;

        // step4: 当前节点的前一个节点指针设置为null
        foundNode.prev = null;

        /*
        
        第一步(原链表, [prev:0:next]表示原head):
        
           [prev:3:next]       ->        [prev:4:next]         ->       [prev:5:next]
                                               ↑
                                           foundNode
                                              
        
        第二步(当前节点的指针拆解):
        
                     ┌┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┐
           [prev:3:next]       ->        [prev:4:next]         ->       [prev:5:next]
                                            └┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┘
           

               
        第三步(形成新的指针):  
               
           [prev:3:next]       ->        [prev:4:next]         ->       [prev:5:next]
                    ↑                                                      ↑
                  节点5                                                   节点3
    
    
        第四步(节点4将被作为 "新节点" 移动到头部): 
        
                                                  
 
           [prev:4:next]    ->   [prev:0:next]   ->  [prev:3:next]   ->    [prev:5:next]
                 ↑                     ↑                      ↑              ↑
          新head(foundNode)          原head                  节点5           节点3
    
    
        */

        
        //--------------
        return foundNode.value;
    }

}


// 注意缓存cache输出的顺序，和哈希表的有区别

const cache = new LRU(2); // 缓存容量为2
cache.set("red", "red"); //缓存 {red=red}
cache.set("grey", "grey"); //缓存 {grey=grey, red=red}
//----

const param_1 = cache.get("red");
console.log(param_1); // red (从缓存中获取)
//----

cache.set("yellow", "yellow"); // 添加新键，超过了容量，则grey将被淘汰，缓存有 {yellow=yellow, red=red}
//----

const param_2 = cache.get("grey");
console.log(param_2); // undefined
//----
```





### 两个栈实现队列
----

我们已经有一个带有 push 和 pop 操作的栈数据结构，任务是使用栈数据结构的实例和对它们的操作来实现一个队列。 一个队列可以使用两个栈来实现。 

设要实现的队列为 q，用于实现 q 的堆栈为 stack1 和 stack2。 (操作这两个“先进后出”的栈实现一个“先进先出”的队列。)


#### 数据结构

```base
栈(Stack)   

插入和删除都发生在尾部

                                  push
                                  ↓
 [ ][ ][ ][ ][ ][ ][ ][ ][ ][ ][ ]
                                ↓
                                top
 
 
 
 

队列(Queue)

   入队enqueue                          出队dequeue
     ↓                                  ↓
      [ ][ ][ ][ ][ ][ ][ ][ ][ ][ ][ ]
       ↓                             ↓
     rear                           front


```

 
 
#### 解法
```js
class Queue {
    constructor() {
        this.s1 = [];
        this.s2 = [];
    }

    enQueue(x) {
        // 将所有元素从 s1 移动到 s2
        while (this.s1.length != 0) {
            this.s2.push(this.s1.pop());
            //s1.pop(); 
        }

        // 将元素推入 s1
        this.s1.push(x);

        // 将所有内容推回 s1
        while (this.s2.length != 0) {
            this.s1.push(this.s2.pop());
            //s2.pop(); 
        }
    }

 
    deQueue() {
        // 如果第一个栈为空
        if (this.s1.length == 0) {
            console.log( '队列为空' );
        }

        // 返回 s1 的顶部
        let x = this.s1[this.s1.length - 1];
        this.s1.pop();
        return x;
    }
}


let q = new Queue();
q.enQueue(1);
q.enQueue(2);
q.enQueue(3);

console.log( JSON.stringify(q));  // {"s1":[3,2,1],"s2":[]}
//-----

q.deQueue();
console.log( JSON.stringify(q));  // {"s1":[3,2],"s2":[]}
//-----

q.deQueue();
console.log( JSON.stringify(q));  // {"s1":[3],"s2":[]}
//-----
```





### 二叉树层序遍历
----

已知二叉树的根节点 root ，返回其节点值的层序遍历 。 （即逐层地，从左到右访问所有节点, 广度优先遍历）。


```base
例：

     3
    / \
   9   20
       / \
     15   7
     
     
输入：root = [3,9,20,null,null,15,7]
输出：[[3],[9,20],[15,7]]
 
```

 

#### 数据结构

```base
队列(Queue)

   入队enqueue                          出队dequeue
     ↓                                  ↓
      [ ][ ][ ][ ][ ][ ][ ][ ][ ][ ][ ]
       ↓                             ↓
     rear                           front

```

#### 过程(使用队列)

```base
把数据按照队列的数据结构从左到右表示出来，即：


         [最先插入的节点]                  
              ↓                                
 (出队) <--  [ 3 ][ 9 ][ 20 ][ NULL ][ NULL ][ 15 ][ 7 ] <-- (入队)
            


注意：每一层的元素(包括null)，其实都是 2 的倍数，可以结合考虑 % 2 余数的算法

Level 1: [3] 
Level 2: [9, 20] 
Level 3: [null, null, 15, 7] 

▲ 代表"删除的节点"



第一步：

                                 
       <--  [ 3 ] <--
              ▲         
                        



第二步：

                    
                                           
        <--   [ 3 ][ 9 ][ 20 ][ NULL ][ NULL ] <--
                ▲    ▲     
                        
                        
 第三步：

                    
                                           
        <--   [ 3 ][ 9 ][ 20 ][ NULL ][ NULL ][ 15 ][ 7 ] <--
                ▲    ▲    ▲                        


```


#### 解法
```js
class Node {
    constructor(val) {
        this.data = val;
        this.left = null;
        this.right = null;
    }
}

let root = null;
root = new Node(3);
root.left = new Node(9);
root.right = new Node(20);
root.right.left = new Node(15);
root.right.right = new Node(7);


// 第一种输出格式
//------------------------
// 计算树的"高度" (从根节点到最远叶节点的最长路径上的节点数)
/*
高度计算：

         3 (h=3)
        / \
 (h=1) 9   20 (h=2)
           / \
    (h=1) 15  7 (h=1)

*/
function height(root) {
    if (root == null) {
        return 0;  // 如果返回 -1， 则高度将 -1，即总高度为 2
    } else {
        // 计算每个子树的高度
        const lheight = height(root.left);
        const rheight = height(root.right);

        // 使用较大的
        if (lheight > rheight) {
            return (lheight + 1);
        } else {
            return (rheight + 1);
        }
        
        /*
        也可将上面的代码简写成一行：
        return Math.max( height(root.left), height(root.right) ) + 1;
        */
            
    }
}

// 遍历每一层
function traversalCurrentLevel(root, level) {
    if (root == null) {
        return 'null';
    }

    if (level == 1) {
        return root.data;
    }

    if (level > 1) {
        return String(traversalCurrentLevel(root.left, level - 1)) + ',' +
               String(traversalCurrentLevel(root.right, level - 1));
    }
}

function levelOrder(root) {
    const h = height(root);
    let res = '';
    for (let i = 1; i <= h; i++) {
        res += '[';
        res += traversalCurrentLevel(root, i) 
        res += ']';

        //末尾去掉逗号
        if (i !== h) res += ',';
    }

    return JSON.parse(`[${res}]`);
}


console.log( levelOrder(root) );  // [[3], [9, 20], [null, null, 15, 7]]




// 第二种输出格式 (使用队列分组)
//------------------------
// 队列原理：相当于根据当前 level 交替推送两个队列中的节点
function levelOrder(root) {
    let res = [], 
        queue = [];
    queue.push(root); // [Node]

    if(root === null) return res;

    while(queue.length !== 0) { // 相当于每次 level 的循环，直到 queue为空时停止循环

        // 队列中的所有元素都是 null,则直接退出 while 循环
        const queueAllElementsAreNull = queue.every( (item) => item === null );
        if ( queueAllElementsAreNull ) break;


        // 记录当前层级节点数
        let currentLevelSize = queue.length;

        //存放每一层的节点 
        let curLevel = [];
        for(let i = 0; i < currentLevelSize; i++) {
            let tempNode = queue.shift();

            if (tempNode) {
                curLevel.push(tempNode.data);
    
                // 存放当前层下一层的节点
                queue.push(tempNode.left);
                queue.push(tempNode.right);

            } else {
                curLevel.push(null);
            }


            /*
            如果不想把 null 添加到结果中，可以删除 queueAllElementsAreNull 条件，并把 if (tempNode) {...}else{...} 替换成下面的代码：
            
            curLevel.push(tempNode.data);

            // 存放当前层下一层的节点
            if (tempNode.left != null) queue.push(tempNode.left);
            if (tempNode.right != null) queue.push(tempNode.right);
            */
            
        }
        //把每一层的结果放到结果数组
        res.push(curLevel);
    }
    return res;
}

console.log( levelOrder(root) );  // [[3], [9, 20], [null, null, 15, 7]]

```





### 无重复最长子串
----

给定一个字符串，请你找出其中不含有重复字符的最长子串的长度。


示例1:

 - 输入: "abcabcbb"
 - 输出: 3 
 - 解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。


示例2:

 - 输入: "bbbbb"
 - 输出: 1
 - 解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
 


#### 过程
```base

  
              [ a ][ b ][ c ][ a ][ b ][ c ][ b ][ b ]
                ↑    ↑    ↑    ↑    ↑    ↑    ↑    ↑
计数i/start:     0    0    0    1    2    3    5    7
结果变化:         1    2    3    3    3    3    3    3
                ——   ——   ——   ——   ——   ——   ——   ——
循环编号:         0    1    2    3    4    5    6    7
    
    
1. 当循环编号=3时，节点a已经被访问过，所以 i/start 更新为 0+1=1  
  1-1. 使用哈希表时，第二个 a 节点值为 0 (当end=0时就被赋值过)；
  1-2. 使用线性时间时，第二个 a 的值因为是 lastIndex[str.charCodeAt(3)]=0 (当j=0时就被赋值过)

2. 当循环编号>3时。将使用新的 j/end 减去变化的 i/start，与之前的结果做比对。

```



#### 解法
```js
// 基本解法(时间复杂度 O(n^2) )
//------------------------
function longestUniqueSubstr(str) {
    const n = str.length;
    let res = 0;

    for (let i = 0; i < n; i++) {

        // 注意：visited 中的默认值为 false
        const visited = new Array(256);

        for (let j = i; j < n; j++) {

            // 如果当前字符被访问，则跳出循环
            // charAt() 是按位置返回字符；charCodeAt() 是按位置返回对应字符的Unicode编码
            if (visited[str.charCodeAt(j)] == true) {
                break;
            } else {
                res = Math.max(res, j - i + 1);
                visited[str.charCodeAt(j)] = true;
            }
        }
    }
    return res;
}



// 使用线性时间(时间复杂度 O(n + d))
//------------------------
function longestUniqueSubstr2(str) {
    const n = str.length;
    let res = 0;

    // 所有字符的最后一个索引被初始化为 -1
    const lastIndex = new Array(256).fill(-1); 
 
    // 初始化当前数据的开始
    let i = 0;

    // 移动当前数据的结尾
    for (let j = 0; j < n; j++) {

        // 找到 str[j] 的最后一个索引
        // 将 i（当前数据的起始索引）更新为 i 的当前值和最后一个索引 +1 的最大值
        i = Math.max(i, lastIndex[str.charCodeAt(j)] + 1);

        // 如果我们得到更大的数据，则更新结果
        res = Math.max(res, j - i + 1);

        // 更新 j 的最后一个索引
        lastIndex[str.charCodeAt(j)] = j;
    }
    return res;
}


// 使用哈希表(时间复杂度 O(n + d))
//------------------------
function longestUniqueSubstr3(s) {
    let seen = new Map();
    let maximumLength = 0;

    // 设置开始数据的初始点索引
    let start = 0;

    for (let end = 0; end < s.length; end++) {

        if (seen.has(s[end])) {

            // 如果我们查找到目标，将开始指针移动到最后一次出现之后的位置
            start = Math.max(start, seen.get(s[end]) + 1);
        }

        // 更新最后一次查找到的值
        seen.set(s[end], end)
        maximumLength = Math.max(maximumLength, end - start + 1);
    }
    return maximumLength;
}

console.log( longestUniqueSubstr3( 'abcabcbb' ) ); // 3  =>  abc
console.log( longestUniqueSubstr3( 'geeksforgeeks' ) ); // 7  => ksforge
```














