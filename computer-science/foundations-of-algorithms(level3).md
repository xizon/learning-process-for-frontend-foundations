# 常见算法基础(Level 3)

## 目录

 - [二分搜索](#二分搜索)
 - [内插搜索](#内插搜索)
 - [随机排序](#随机排序)
 - [回溯算法(迷宫老鼠问题)](#回溯算法迷宫老鼠问题)
 - [回溯算法(数独解题器)](#回溯算法数独解题器)
 - [贪心算法(最少硬币找零问题)](#贪心算法最少硬币找零问题)
 - [贪心算法(分数背包问题)](#贪心算法分数背包问题)
 - [动态规划(最少硬币找零问题)](#动态规划最少硬币找零问题)
 - [动态规划(背包问题)](#动态规划背包问题)
 - [动态规划(最长公共子序列)](#动态规划最长公共子序列) 
 - [动态规划(矩阵链相乘)](#动态规划矩阵链相乘) 
 

### 二分搜索
----

比如“我正想着一个1~100的数”的游戏，每回应一个数，另一个人说这个数是高了还是低了。

**原理：**

1. 先对数据排序
2. 选择数组中间的值的索引(使用最小和最大两个边界临时的索引来计算中间索引)
3. 如果中间值是搜索值，则结束
4. 如果目标值小于中间值，则返回步骤2(重新计算中间索引)，并在中间值"左边"（中间值索引+1）的子数组中寻找(较小)
5. 如果目标值大于中间值，则返回步骤2(重新计算中间索引)，并在中间值"右边"（中间值索引-1）的子数组中寻找(较大)

#### 算法步骤
```js
/*
过程：

原数据： [5,3,1,7,6,4,2], 目标：6

排序后： [1,2,3,4,5,6,7], low=0, high=7-1=6

第一次循环：中间值：mid = (0+6)/2 = 3,  arr[3]=4
    
    由于值 6 > 4，mid索引+1, arr[3+1]=5
    
    
第二次循环：中间值：mid = (4+6)/2 = 5,  arr[5]=6

    由于值 6 = 6，所以直接返回
      
    
【如果是顺序搜索，则需要迭代每一个元素，循环次数就变多了】

*/
```

#### 解法：
```js

function binarySearch(array, value) {
    const sortedArray = array.sort((a,b) => {
 		    return a-b
	});  // 不要直接写sort()，否则个位数和十位数将不能正常排序，可以使用其他算法排序， [1, 2, 3, 4, 5, 6, 7]


    //设置low和high指针（索引），它们是边界
    let low = 0;  
    let high = sortedArray.length - 1;

    while (low <= high) {
        const mid = Math.floor((low + high) / 2); //计算中间指针
        const element = sortedArray[mid];

        if (element < value) {
            low = mid + 1;
        } else if (element > value) {
            high = mid - 1;
        } else {
            // 目标值就是中间值，找到了
            return mid;
        }
    }
    return -1;
}


console.log( binarySearch([5,3,1,7,6,4,2], 6) ); // 索引：5
```

#### 复杂度计算：

算法复杂度是while循环的次数：

 - 第1次，在n个元素内找到目标
 - 第2次，在n/2个元素内找到目标
 - 第3次，在n/(2^2)个元素内找到目标
 - ...
 - 第k次，在n/(2^k)个元素内找到目标

因为 2^k <= n，所以当且仅当 n/(2^k) >= 1时，k最大为 log2(n) (2为底)


**-->时间复杂度： O(log(n)) 其中底数为2**


### 内插搜索
----

内插搜索是改良版的二分搜索，二分搜索是检查mid位置上的值，内插搜索是根据搜索的值检查数组中的不同地方。


**原理：**

1. 先对数据排序
2. 使用公式计算position，选中一个值的索引(使用最小和最大两个边界临时的索引来计算选中索引)
3. 如果选中值是搜索值，则结束
4. 如果目标值小于选中值，则返回步骤2(重新计算选中索引)，并在选中值"左边"（选中值索引+1）的子数组中寻找(较小)
5. 如果目标值大于选中值，则返回步骤2(重新计算选中索引)，并在选中值"右边"（选中值索引-1）的子数组中寻找(较大)

**核心的算法：**

 - position公式 = 最小索引 + (最大索引和最小索引之间的范围) * 偏移量
 - 偏移量是动态改变的，所以需要使用一个动态的数字：value
 - 偏移量delta一定要小于或等于0，所以实际上也可以简写为 let delta = value / sortedArray[high]
 - 每次递归如果没找到结果，偏移量就一定会逐渐变小



#### 算法步骤
```js
/*
过程：

和二分搜索过程一致，相当于mid值经过了position的位置算法动态变化。


*/
```


#### 解法1：

使用递归
```js

function interpolationSearch(array, low, high, value) {
    const sortedArray = array.sort((a,b) => {
 		    return a-b
	});  // 不要直接写sort()，否则个位数和十位数将不能正常排序，可以使用其他算法排序， [1, 2, 3, 4, 5, 6, 7]

    let position;

    // 数组中必须在边界范围内
    if (low <= high && value >= sortedArray[low] && value <= sortedArray[high]) {

        // 增量 
        //递归的值如：  0.8333333333333334, 0.21621621621621623,  0
        let delta = (value - sortedArray[low]) / (sortedArray[high] - sortedArray[low]);  // 也可以写成 value / sortedArray[high]
        
        position = low + Math.floor((high - low) * delta);

        // 目标已找到
        if (sortedArray[position] == value) {
            return position;
        }

        // 如果value较大，则value在右子数组中
        if (sortedArray[position] < value) {
            return interpolationSearch(sortedArray, position + 1, high, value);
        }

        // 如果value较小，则value在左子数组中
        if (sortedArray[position] > value) {
            return interpolationSearch(sortedArray, low, position - 1, value);
        }
    }
    return -1;
}

let arr = [5,3,1,7,6,4,2];
console.log( interpolationSearch(arr, 0,  arr.length-1, 6) ); // 索引：5 【注意这个索引是排序后数组的索引】

```

#### 解法2：

使用while循环

```js
function interpolationSearch(array, value) {
    const sortedArray = array.sort((a,b) => {
 		    return a-b
	  });  // 不要直接写sort()，否则个位数和十位数将不能正常排序，可以使用其他算法排序， [1, 2, 3, 4, 5, 6, 7]

    let low = 0;
    let high = array.length - 1;
    let position = -1;
 
    // 数组中必须在边界范围内
    while (low <= high && value >= sortedArray[low] && value <= sortedArray[high]) {

        // 增量 
        //递归的值如：  0.8333333333333334, 0.21621621621621623,  0
        let delta = (value - sortedArray[low]) / (sortedArray[high] - sortedArray[low]);  // 也可以写成 value / sortedArray[high]
        position = low + Math.floor((high - low) * delta);

        // 目标已找到
        if (sortedArray[position] == value) {
            return position;
        }

        // 如果value较大，则value在右子数组中
        if (sortedArray[position] < value) {
            low = position + 1;
        }

        // 如果value较小，则value在左子数组中
        if (sortedArray[position] > value) {
            high = position - 1;
        }
    }
    return -1;
}

  
console.log( interpolationSearch([5,3,1,7,6,4,2], 6) ); // 索引：5 【注意这个索引是排序后数组的索引】
```

**-->时间复杂度： O(n * log(n)) 其中底数为2**


### 随机排序
----

也叫 **Fisher–Yates随机算法**。类似洗扑克牌。

1. 迭代数组，从最后一位开始并将当前位置和一个随机位置进行交换
2. 这个随机位置比当前位置小(保证随机过的位置不会再次随机一次)


#### 算法步骤
```js
/*
过程：

Math.random()值可能为：0.1623896825850839， 0.7994312104639176 ...

原数据：[5,3,1,7,6,4,2]

第一次循环： currentIndex=6, randomIndex=2，
                  ┌┈┈┈┈┈┈┈┈┈┈┈┐
      结果： 5  3  2  7  6  4  1

第二次循环： currentIndex=5, randomIndex=4，

                        ┌┈┈┐
      结果： 5  3  2  7  4  6  1
      
      
第三次循环： currentIndex=4, randomIndex=1，

            ┌┈┈┈┈┈┈┈┈┈┈┈┐
      结果： 4  3  2  7  5  6  1   
      
      
...同理接下去的循环



*/
```


#### 解法1：
```js
function shuffle(array) {
    let currentIndex = array.length;
    while (currentIndex !== 0) {
        const randomIndex = Math.floor(Math.random() * currentIndex);
        currentIndex--;
        
        //交换元素
        [array[currentIndex], array[randomIndex]] = [array[randomIndex], array[currentIndex]];
    }
    return array;
}


  
console.log( shuffle([5,3,1,7,6,4,2]) ); // [7, 2, 6, 1, 5, 3, 4],  [1, 5, 6, 3, 2, 7, 4] , ...
```

**-->时间复杂度： O(n)**


#### 解法2：

使用for循环
```js
function shuffle(array) {
    for (let currentIndex = array.length-1;currentIndex > 0; currentIndex--) {
         const randomIndex = Math.floor(Math.random() * currentIndex);
         [array[currentIndex], array[randomIndex]] = [array[randomIndex], array[currentIndex]];
    }
    return array;
}
```



### 回溯算法(迷宫老鼠问题)
----

回溯(huí sù)是一种渐进式寻找并构建问题解决方案的策略。从一个可能的动作开始并试着用它解决问题。如果不能解决，就回溯并选择另一个动作直到问题解决。


#### 深入理解

 - **1.概念：** 回溯算法是一个类似枚举的搜索尝试过程，若不满足求解条件，就返回，尝试别的路径。

 - **2.和递归的区别：**

    - 回溯：从问题"本身"出发，寻找可能实现的所有情况。
    
    - 递归：从问题的"结果"出发，例如求n!,就要知道n*(n-1)!...， 不断调用自己。

 - **3.过程：** 建立状态树。对于每种元素，都有两种状态(true 或 false)




#### 问题描述


假设我们有一个大小为 N * N 的矩阵，矩阵的每个位置时一个方块。每个位置(或块)可以是空闲的(值为1)或者被阻拦的(值为0)，如下图所示，Start为起点，End为终点。
矩阵就是迷宫，老鼠的目标是从 [0][0]开始并移动到 [n-1][n-1] 的终点。老鼠可以在垂直或水平方向任何未被阻挡的位置移动。

```base
---------------------------------
| Start |       |       |       |
|       |       |       |       |
|       |       |       |       |
|       |       |       |  End  |
---------------------------------
```


**解决思路：**

1. 矩阵就相当于XY坐标
2. 寻找路径要使用递归（完成回溯）




#### 算法步骤
```js
过程：

迷宫为：
--------------------
|  1   0   0   0   |
|  1   1   1   1   |
|  0   0   1   0   |
|  0   1   1   1   |
--------------------


找到的路径为：
--------------------
|  1   0   0   0   |
|  ↓               |
|  1 → 1 → 1   1   |
|          ↓       |
|  0   0   1   0   |
|          ↓       |
|  0   1   1  → 1  |
--------------------


结果为(只保留1为合法路径)：
--------------------
|  1   -   -   -   |
|  1   1   1   -   |
|  -   -   1   -   |
|  -   -   1   1   |
--------------------

```

#### 解法：
```js
//判断是否是安全的路径 (x和y的坐标要小于整个矩阵的行数和列数, 且坐标值不能是 0)
function isSafe(maze, x, y) {
    const n = maze.length;
    if (x >= 0 && y >= 0 && x < n && y < n && maze[x][y] !== 0) {
        return true;
    }
    return false;
}

//查找路径（回溯递归）
function findPath(maze, x, y, res) {
    const n = maze.length;

    // 验证是否到达终点，如果到了就将最后一个位置标记为 true，表示移动结束
    //-------------
    if (x === n - 1 && y === n - 1) {
        res[x][y] = 1;
        return true;
    }

    // 如果不是最后一步
    //-------------
    if (isSafe(maze, x, y) === true) {
        res[x][y] = 1; // 加入路径
        if (findPath(maze, x + 1, y, res)) {  // 向右移动
            return true;
        }
        if (findPath(maze, x, y + 1, res)) {  // 向下移动
            return true;
        }

        // 路径标记为 false
        res[x][y] = 0;
        return false;
    }

    return false;
}


function ratInAMaze(maze) {
    const res = [];

    //初始化矩阵(横纵坐标)
    for (let i = 0; i < maze.length; i++) {
        res[i] = [];
        for (let j = 0; j < maze[i].length; j++) {
            res[i][j] = 0;
        }
    }
    if (findPath(maze, 0, 0, res) === true) {
        return res;
    }

    return '未找到路径';
}


//初始化迷宫
const maze = [
    [1, 0, 0, 0],
    [1, 1, 1, 1],
    [0, 0, 1, 0],
    [0, 1, 1, 1]
];

console.log( ratInAMaze(maze) );
/*
[
    [1, 0, 0, 0],
    [1, 1, 1, 0],
    [0, 0, 1, 0],
    [0, 0, 1, 1]
]
*/

```

#### 复杂度计算：

**-->时间复杂度：O(2^(n^2))  递归可以运行上限 2^(n^2) 次**

需要输出矩阵，因此需要一个大小为 n*n 的额外空间。空间复杂度可以设为：O(n^2)



### 回溯算法(数独解题器)
----


#### 问题描述

数独[shù dú]（英文：Sudoku）是源自18世纪瑞士的一种数学游戏。是一种运用纸、笔进行演算的逻辑游戏。

**数独游戏规则：** 标准数独是由一个给与了提示数字的9x9网格组成，每行、列、宫各自都要填上1-9的数字，要做到每行、列、宫里的数字都不重复。宫是由3×3的小格子组成的。

**数独基础解法：**

 - 唯一余数法： 点算某格的等位群格位中已经出现过哪些数，如果已经出现1 – 9中的8格，那么这格就是第9个数，此数被称为唯一余数。
 - 排除法：就是利用数独中行、列和宫内不能填入相同数字的规则，利用已出现的数字对同行、同列和同宫内其他格进行排斥相同数字的方法。


等位群格位: 指与某格同行、同列、同宫以及附加规则要求1-9不重复的附加区域（如对角线数独中的对角线等）的其他格。

```base

"唯余法"步骤：

1. 寻找横向和纵向的交叉行列，看看它们有哪两个交叉行列有8个数字
2. 剩下的那唯一一个数字填上，即可解决一次
3. 优先处理有8个数字的交叉行列

如下图：先找出D行和4列的交叉情况，共有8个数字，剩下一个(D4位置)填5。

  1  2  3   4  5  6   7  8  9
 -------------------------------
A| -  -  - | 6  -  - | -  -  - | 
B| -  -  - | -  -  - | -  -  - | 
C| -  -  - | -  -  - | -  -  - | 
 | --------------------------- | 
D| 1  -  2 |[5] -  3 | -  -  4 | 
E| -  -  - | 7  -  - | -  -  - | 
F| -  -  - | -  -  - | -  -  - | 
 | --------------------------- | 
G| -  -  - | 8  -  - | -  -  - | 
H| -  -  - | -  -  - | -  -  - | 
I| -  -  - | 9  -  - | -  -  - | 
 -------------------------------

```



#### 算法步骤
```base
过程：

81个空格如下：

-------------------------------
| -  -  - | -  -  - | -  -  6 | 
| -  3  - | -  7  1 | -  4  - | 
| -  -  - | -  -  - | 8  -  - | 
| --------------------------- | 
| -  -  - | 9  -  8 | -  7  1 | 
| 1  -  3 | -  -  - | -  -  - | 
| -  -  2 | -  3  - | 9  -  - | 
| --------------------------- | 
| 5  -  7 | -  -  6 | -  -  - | 
| 2  -  - | -  -  - | 7  -  - | 
| -  -  1 | 8  -  - | -  -  2 | 
-------------------------------



结果为：
-------------------------------
| 7  2  4 | 3  8  9 | 1  5  6 |  
| 6  3  8 | 5  7  1 | 2  4  9 |   
| 9  1  5 | 6  4  2 | 8  3  7 |  
| --------------------------- | 
| 4  5  6 | 9  2  8 | 3  7  1 | 
| 1  9  3 | 7  6  4 | 5  2  8 | 
| 8  7  2 | 1  3  5 | 9  6  4 | 
| --------------------------- | 
| 5  8  7 | 2  9  6 | 4  1  3 | 
| 2  6  9 | 4  1  3 | 7  8  5 | 
| 3  4  1 | 8  5  7 | 6  9  2 | 
-------------------------------

```


#### 解法：
```js

//索引 => 行列坐标
function i2rc(index) {
    return { row: Math.floor(index / 9), col: index % 9 };
}

//行列坐标 => 索引
function rc2i(row, col) {
    return row * 9 + col;
}

// 有哪些可用选项，即在给定单元格中可以接受哪些数字（也将它们称为“移动”）
function getChoices(board, index) {
    let choices = [];
    for (let value = 1; value <= 9; ++value) {
        if (acceptable(board, index, value)) {
            choices.push(value);
        }
    }
    return choices;
}


// 棋盘是我们的数组，包含81个元素，此函数将返回当前在该索引的单元格上允许的数字数组。
// 拼图的实际规则是在函数acceptable()中实现的：我们可以使用未在同一行、同一列或同一 3x3 正方形中使用的数字
function acceptable(board, index, value) {
    let { row, col } = i2rc(index);

    // 列上已经存在，则不可接受
    for (let r = 0; r < 9; ++r)
        if (board[rc2i(r, col)] == value) return false;

    // 行上已经存在，则不可接受
    for (let c = 0; c < 9; ++c)
        if (board[rc2i(row, c)] == value) return false;

    // 如果已经出现在同一个 3x3 方格中，也不能接受
    let r1 = Math.floor(row / 3) * 3;
    let c1 = Math.floor(col / 3) * 3;
    for (let r = r1; r < r1 + 3; ++r) {
        for (let c = c1; c < c1 + 3; ++c) {
            if (board[rc2i(r, c)] == value) return false;
        }
    }

    return true;
}


//回溯检查问题是否解决
function solve(board) {

    // 遍历每个索引
    // 再次为每个字段寻找填充和计算移动的最佳位置 
    //-----------------
    let index, moves;
    
    for (let i = 0; i < board.length; ++i) {
        if (!board[i]) {

            let m = getChoices(board, i);
            moves = m;
            index = i;

            if (m.length === 0) break;
        }
    }
 
    //回溯检查问题是否解决
    //-----------------
    if (index == null) return true;

    for (let m of moves) {
        // 尝试一种选择
        board[index] = m;

        // 如果我们能解决下一个单元格
        if (solve(board)) return true;
    }
    
    board[index] = 0;  // 没有任何动作；失败并清除单元格
    return false;      // 回溯

}


function sudokuSolver(matrix) {
    if (solve(matrix) === true) {
        return matrix;
    }
    return '不能找到解决方案';
}


const myBoard = [
    0, 0, 0,  0, 0, 0,  0, 0, 6,
    0, 3, 0,  0, 7, 1,  0, 4, 0,
    0, 0, 0,  0, 0, 0,  8, 0, 0,

    0, 0, 0,  9, 0, 8,  0, 7, 1,
    1, 0, 3,  0, 0, 0,  0, 0, 0,
    0, 0, 2,  0, 3, 0,  9, 0, 0,

    5, 0, 7,  0, 0, 6,  0, 0, 0,
    2, 0, 0,  0, 0, 0,  7, 0, 0,
    0, 0, 1,  8, 0, 0,  0, 0, 2,
];

console.log( sudokuSolver(myBoard) );
/*
结果：
[
    7, 2, 4,  3, 8, 9,  1, 5, 6, 
    6, 3, 8,  5, 7, 1,  2, 4, 9, 
    9, 1, 5,  6, 4, 2,  8, 3, 7, 
    
    4, 5, 6,  9, 2, 8,  3, 7, 1, 
    1, 9, 3,  7, 6, 4,  5, 2, 8, 
    8, 7, 2,  1, 3, 5,  9, 6, 4, 
    
    5, 8, 7,  2, 9, 6,  4, 1, 3, 
    2, 6, 9,  4, 1, 3,  7, 8, 5, 
    3, 4, 1,  8, 5, 7,  6, 9, 2
]
*/


```

#### 复杂度计算：

**-->时间复杂度：O(9^(n*n))**

需要输出矩阵，因此需要一个大小为 n*n 的额外空间。空间复杂度可以设为：O(n^2)



### 贪心算法(最少硬币找零问题)
----

是一种近似解决问题的技术，期盼通过每个阶段的局部最优选择(当前最好的解)，从而达到全局的最优(全局最优解)。

它不像动态规划算法那样计算更大的格局（动态规划*dynamic programming是一种将复杂问题分解成更小的子问题来解决的优化技术）


#### 问题描述

给出要找零的钱数，以及可以使用的面额及其数量，找出有多少种找零方法。

#### 算法步骤
```js
/*
过程：

面额：[1,5,10,25]，目标值：36

第一次：

   25(面额) + 0(total) = 25
   
   由于25 < 36, 执行while里面的代码
   
   
第二次：

   10(面额) + 25(total) = 35
   
   由于35 < 36, 执行while里面的代码
   
   
第三次：

   5(面额) + 35(total) = 40
   
   由于40 > 36, 跳出while
   
   
第四次：

   1(面额) + 35(total) = 36
   
   36 = 36, 执行while里面的代码，结束
   

*/
```

#### 解法：
```js
function minCoinChange(coins, amount) {
    const change = [];
    let total = 0;
    for (let i = coins.length-1; i >= 0; i--) {
        const coin = coins[i];
        while (total + coin <= amount) { //对每个面额，把它的值total相加后，total需要小于amount
            change.push(coin); //将当前面额添加到结果中
            total += coin;
        }
    }
    return change;
}

  
console.log( minCoinChange([1,5,10,25], 36) );  // [25, 10, 1]
console.log( minCoinChange([1,3,4], 6) );  // [4, 1, 1]
```

**-->时间复杂度： O(n * k) k为面额的数量**


### 贪心算法(分数背包问题)
----

#### 问题描述

给定一个固定大小、能够携重量 W 的背包，以及一组有价值和重量的物品，找出一个最佳解决方案，使得装入背包的物品重量不超过 W，且总价值最大。
【考虑分数】

#### 算法步骤

```js
/*
过程：
-----------------------------------
|   物品    |    重量   |    价值   |
|   1      |    2     |     3     |
|   2      |    3     |     4     |
|   3      |    4     |     5     |
-----------------------------------

容量(总重量)为6

第一次： 重量=2， 价值=3
第二次： 重量=2+3=5， 价值=3+4=7
第三次： 重量=5+1[(6-5)/4,相当于物品3的0.25倍]=6，价值=7+(5*0.25)=8.25

*/
```

#### 解法：

```js
function knapSack(capacity, weights, values) {
    const n = values.length;
    let load = 0; //已经装载的重量
    let val = 0;  //物品的价值
    for (let i = 0; i < n && load < capacity; i++) {
        if (weights[i] <= capacity - load) {
            val += values[i];
            load += weights[i];
        } else {
            const r = (capacity - load) / weights[i];  //平分某个物品
            val += r * values[i];
            load += weights[i];
        }
    }
    return val;
}

console.log( knapSack(6, [2,3,4], [3,4,5]) ); // 总价值 8.25
```


**-->时间复杂度: O(n)**



### 动态规划(最少硬币找零问题)
----

动态规划(dynamic programming，**DP**) 是一种将复杂问题分解成更小的子问题来解决的优化技术.

步骤：

1. 定义子问题
2. 实现要反复执行来解决子问题的部分(递归)
3. 识别并求出基线条件。



#### 问题描述

给出要找零的钱数，以及可以使用的面额及其数量，找出有多少种找零方法。

#### 算法步骤
```js
/*
过程：

面额：[1,3,4]，目标值：6

这不是一个将返回值作为参数传递到函数的递归，和阶乘的递归有所区别。

(1) minCoinChange(coins, 6, cache)

(2) minCoinChange(coins, 5, cache)

(3) minCoinChange(coins, 3, cache)

(4) minCoinChange(coins, 2, cache)


先从(4)开始计算，一直到(1)，amount将从小到大分别赋值，直到cache[6]，即目标值


计算过程：

第一步：minCoinChange(coins, 2, cache)

   newAmount分别为 1,-1,-2,  继续递归  minCoinChange(coins, 1, cache)
   
   minCoinChange(coins, 1, cache) 的结果为： coin = 1 =>  newRes = [] , res = [1]
   minCoinChange(coins, 2, cache) 的结果为： coin = 1 =>  newRes = [1] , res = [1,1]
   

第二步：minCoinChange(coins, 3, cache)
  
    newAmount分别为 2,0,-1,  继续递归  minCoinChange(coins, 2, cache) 和 minCoinChange(coins, 0, cache)
    
    minCoinChange(coins, 0, cache) 的结果为： 直接返回 []
    minCoinChange(coins, 2, cache) 的结果为： coin = 1 =>  newRes = [1] , res = [1,1]  【第一步已经计算过】
  
   
第三步：minCoinChange(coins, 5, cache)
   
     newAmount分别为 4,2,1,  继续递归  minCoinChange(coins, 4, cache) minCoinChange(coins, 2, cache) 和 minCoinChange(coins, 1, cache)
   
    minCoinChange(coins, 1, cache) 的结果为： coin = 1 =>  newRes = [] , res = [1]  【第一步已经计算过】  
    minCoinChange(coins, 2, cache) 的结果为： coin = 1 =>  newRes = [1] , res = [1,1]  【第一步已经计算过】
    minCoinChange(coins, 4, cache) 中，newAmount分别为 3,1,0 奖继续拆分递归  minCoinChange(coins, 3, cache) minCoinChange(coins, 1, cache) 和 minCoinChange(coins, 0, cache)
    
    (...省略)
    
第四步：minCoinChange(coins, 6, cache)

    (...省略)
    

*/
```

#### 解法：
```js
function minCoinChange(coins, amount, cache) {
    if( amount === 0 ){
        return [];
    }
    
    //cache用来记忆化, 如果已经缓存，则直接返回结果；否则，执行算法
    if (cache[amount]) { 
        return cache[amount];
    }

    let res = [];

    //面额循环内使用的结果变量
    let newRes;
    let newAmount;

    for (let i = 0; i < coins.length; i++) {
        const coin = coins[i];
        newAmount = amount - coin;  //对于每个面额，都计算newAmount的值，它的值会一直减小，直到能找零的最小钱数
    
        if (newAmount >= 0) { //迭代可用的面额
            newRes = minCoinChange(coins,newAmount,cache);

            if (newRes.length < res.length - 1 || res.length === 0) { // res.length === 0 为 true, 可以写成 !res.length (相当于 !0 )
                res = [coin].concat(newRes); //与面额组合凑结果，寻找最优解
                console.log(`最小面额组合 [${res}] 总和为: ${amount}`);
            }
        }

    }

    cache[amount] = res; //缓存结果
    return res;
}


  
console.log( minCoinChange([1,3,4], 6, []) );  // [3, 3]
/*
最小面额组合 [1] 总和为: 1
最小面额组合 [1,1] 总和为: 2
最小面额组合 [1,1,1] 总和为: 3
最小面额组合 [3] 总和为: 3
最小面额组合 [1,3] 总和为: 4
最小面额组合 [4] 总和为: 4
最小面额组合 [1,4] 总和为: 5
最小面额组合 [1,1,4] 总和为: 6
最小面额组合 [3,3] 总和为: 6
*/
```

它可以被看做 **NP(nondeterministic polynomial,非确定性多项式)** 算法，即一个问题可以在多项式时间内验证解是否正确，则计为NP。虽然它可以在多项式时间内验证解，但也可能还没找到多项式算法，不能迅速找到最优解，这种问题被称为 **"NP完全问题"**。

本案例是一个NP完全问题(如果输入规模大，那么运行速度会呈指数型增长，运行结果会非常慢，所以使用动态规划是在有限的输入规模内，实现的伪多项式计算。)

**-->时间复杂度：  O(n * m) m为每次迭代需要计算的子项，m的值为m的位数的幂，这个时间复杂度作为伪多项式时间**





### 动态规划(背包问题)
----

#### 问题描述

给定一个固定大小、能够携重量 W 的背包，以及一组有价值和重量的物品，找出一个最佳解决方案，使得装入背包的物品重量不超过 W，且总价值最大。


#### 解决思路：

注意重量和价值是递增的一组数字。

如果当前的容量不够装当前物品，那么肯定只能尝试装n-1的物品。

1. 如果装不下当前物品，那么前n个物品的最佳组合和前n-1个物品的最佳组合是一样的。
2. 如果装得下物品。

    2-1. 装当前物品，如果空间充足，前n-1个物品的最佳组合 + 当前物品的价值就是总价值

    2-2. 不装当前物品。那么前n个物品的最佳组合和前n-1个物品的最佳组合是一样的
    
3. 比较2-1和2-2的值




#### 算法步骤
```js

/*
过程：

-----------------------------------
|   物品   |    重量   |    价值   |
|   1      |    2     |     3    |
|   2      |    3     |     4    |
|   3      |    4     |     5    |
-----------------------------------

容量(总重量)为5

外循环是物品的数量，内循环是容量，形成二维数组

矩阵构造：

(第一行表示背包容量，第一列代表物品号，每一行表示对应列的背包容量下可装入的物品最大价值)


| i/w | 0 | 1 | 2 | 3 | 4 | 5 |
-------------------------------
|  0  | 0   0   0   0   0   0 |
|  1  | 0   -   -   -   -   - |
|  2  | 0   -   -   -   -   - |
|  3  | 0   -   -   -   -   - |



最终赋值后的矩阵：


| i/w | 0 | 1 | 2 | 3 | 4 | 5 |
-------------------------------
|  0  | 0   0   0   0   0   0 |
|  1  | 0   0   3   3   3   3 |
|  2  | 0   0   3   4   4   7 |
|  3  | 0   0   3   4   5   7 |


注意：每一个不同的内循环 (j), 都需要使用上一次的最优结果作为考量(这也是动态规划的思想)

这里过程中的 i, 是对应上面的表格的，并不是代码中的i的取值(代码中的取值相当于多+1)

第一次外循环：i=1， 当前物品重量为2

     [0, 0, 3, 3, 3, 3]

     容量 j 为0/1，均不能装任何物品，最大价值为0
     容量 j 为2/3/4/5，可以装重量为2的当前物品，最大价值为3
     

第二次外循环：i=2， 当前物品重量为3

     [0, 0, 3, 4, 4, 7]

     容量 j 为0/1，均不能装任何物品，最大价值为0
     容量 j 为2，不能装当前物品，使用之前的结果，最大价值为3
     容量 j 为3，可以装重量为2或者3的物品，最大价值为4
     容量 j 为4，可以装重量为2或者3的物品，最大价值为4
     容量 j 为5，可以装重量为2和3的两个物品，最大价值为7

第三次外循环：i=3， 当前物品重量为4

     [0, 0, 3, 4, 5, 7]

     容量 j 为0/1，均不能装任何物品，最大价值为0
     容量 j 为2，不能装当前物品，使用之前的结果，最大价值为3
     容量 j 为3，不能装当前物品，使用之前的结果，最大价值为4
     容量 j 为4，可以装重量为2、3或者4的物品，如果装物品4，则最大价值为5
     容量 j 为5，可以装重量为2和3的两个物品，最大价值为7



*/
```

#### 解法1（迭代法，使用边界）：
```js
function findValues(n, capacity, res) {
    let i = n;
    let k = capacity;
    while (i > 0 && k > 0) {
        if (res[i][k] !== res[i - 1][k]) { // i-1 为当前物品的索引
            console.log(`物品${i}可以是解的一部分: ${weights[i - 1]}(重量) | ${values[i - 1]}(价值)`);
            i--;
            k -= res[i][k];
        } else {
            i--;
        }
    }
}


function knapSack(values, weights, capacity) {
    //res[i][j] 保存背包的最大值,一个矩阵，用来记录结果,矩阵为 res[n+1][capacity+1]
    let res = new Array(values.length + 1);
    for (let i = 0; i < res.length; i++) {
        res[i] = new Array(capacity + 1).fill(0); //初始化矩阵元素默认值为 0
    }

    // 对于第 i 个项目
    for (let i = 1; i <= values.length; i++) {
        // 选择从 0 到最大容量(j 相当于矩阵的纵向列表)
        for (let j = 0; j <= capacity; j++) {
            
            const itemIndex = i - 1; //"之前"的值和"当前"的值，相当于矩阵表中的上下两行
            
            // 基本情况：当前容量 j 如果小于单个物品的重量weights[itemIndex]，则不要选择第 i 个元素
            if (weights[itemIndex] > j) {
                res[i][j] = res[itemIndex][j];
            } else {
                // 存储我们通过选择或离开第 i 个项目获得的最大值
                const a = res[itemIndex][j];
                const b = res[itemIndex][j - weights[itemIndex]] + values[itemIndex]; 
                res[i][j] = Math.max(a, b);
                
                console.log(`物品${i}可以成为解决方案的一部分`);
            }
        }
        console.log(res[i]);
       
    }

    // 输出结果
    findValues(values.length, capacity, res);
    
    // 返回最大值
    return res[values.length][capacity];
}

const values = [3,4,5];
const weights = [2,3,4];
console.log( knapSack(values, weights, 5) ); // 总价值 7

/*
物品1可以成为解决方案的一部分（循环4次）
[0, 0, 3, 3, 3, 3]

物品2可以成为解决方案的一部分（循环3次）
[0, 0, 3, 4, 4, 7]

物品3可以成为解决方案的一部分（循环2次）
[0, 0, 3, 4, 5, 7]


物品2可以是解的一部分: 3(重量) | 4(价值)
物品1可以是解的一部分: 2(重量) | 3(价值)

*/
```

它可以被看做 **NP(nondeterministic polynomial,非确定性多项式)** 算法，即一个问题可以在多项式时间内验证解是否正确，则计为NP。虽然它可以在多项式时间内验证解，但也可能还没找到多项式算法，不能迅速找到最优解，这种问题被称为 **"NP完全问题"**。

本案例是一个NP完全问题(如果输入规模大，那么运行速度会呈指数型增长，运行结果会非常慢，所以使用动态规划是在有限的输入规模内，实现的伪多项式计算。)

**-->时间复杂度：  O(n * m) m为每次迭代需要计算的子项，m的值为m的位数的幂，这个时间复杂度作为伪多项式时间。 也相当于 O(values.length * capacity)**

因为采用了二维数组，空间复杂度为 **O(values.length * capacity)**


#### 解法2（使用递归，无边界，使用迭代的方式需要使用边界(判断背包的有效使用范围)）：

**思路：**

1. 选择当前物品并重复背包容量减少的剩余物品，直到容量变为负数。
2. 将当前物品从背包中取出并重复使用剩余物品。
3. 最后返回我们通过选择或离开当前项目获得的最大值。

```js
function knapSack(values, weights, n, capacity) {
    //基本情况：当我们不能携带更多物品时
    if (capacity < 0) {
        return Number.MIN_SAFE_INTEGER;
    }

    //基本情况：当没有物品或容量变为 0 时
    if (n < 0 || capacity === 0) {
        return 0;
    }
    
    // 选择当前项目 n 并重复剩余项目 (n - 1) 并减少容量 (重量 - 重量[n])
    let include = values[n] + knapSack(values, weights, n - 1, capacity - weights[n]);

    // 离开当前项目 n 并重复剩余项目 (n - 1)
    let exclude = knapSack(values, weights, n - 1, capacity);

    // 返回我们通过选择或离开当前项目获得的最大值
    return Math.max(include, exclude);
}

const values = [3,4,5];
const weights = [2,3,4];
console.log( knapSack(values, weights, values.length-1, 5) ); // 总价值 7
```

**-->时间复杂度：O(2 ^ n)**




### 动态规划(最长公共子序列)


#### 问题描述

最长公共子序列(Longest Common Subsequence, LCS)概念，找出两个字符串序列的最长子序列的长度。它是一个经典的计算机科学问题，也是数据比较程序，比如Diff工具，和生物信息学应用的基础。

最长子序列：在两个字符串序列中以相同的顺序出现，但不要求连续的字符串序列。

子串：和子序列不同，子串必须是连续的。


例如:

 - "ABCDGH" 和 "AEDFHR" =>  ADH
 - "AGGTAB" 和 "GXTXAYB" => GTAB


**解题思路：**

用矩阵来对比两个字符串的每个字符，如果有两个相同的字符，那就算做一次结果。

#### 算法步骤
```js
/*
过程：



先把字符串 "AXYTM" 和 "ANXM" 矩阵化(矩阵化后横向5个元素，纵向4个元素, 下图的字符行和字符列的元素都相当于 0)


| - | A | N | X | M | 
---------------------
| A | 0   0   0   0 |
| X | 0   0   0   0 |
| Y | 0   0   0   0 |
| T | 0   0   0   0 |
| M | 0   0   0   0 |


相当于矩阵(6行5列)：

[0, 0, 0, 0, 0]
[0, 0, 0, 0, 0]
[0, 0, 0, 0, 0]
[0, 0, 0, 0, 0]
[0, 0, 0, 0, 0]
[0, 0, 0, 0, 0]



计算两个相同的字符的矩阵位置（从上至下过程）
【以横向的字符为外循环，每个字符如果和纵向的有重合，结果就累加1】


第一次外循环（字符"AXYTM"的 A）：

   | - | A | N | X | M | 
   ---------------------
   | A | 1   1   1   1 |
   | X | 0   0   0   0 |
   | Y | 0   0   0   0 |
   | T | 0   0   0   0 |
   | M | 0   0   0   0 |

   
   
第二次外循环（字符"AXYTM"的 X）：

   已经存在重合 A = A, 长度为1，内循环字符 "ANXM" 如果有匹配就累加1，存在 X = X
   
   | - | A | N | X | M | 
   ---------------------
   | A | 1   1   1   1 |
   | X | 1   1   2   2 |
   | Y | 0   0   0   0 |
   | T | 0   0   0   0 |
   | M | 0   0   0   0 |
   
   
   
第三次外循环（字符"AXYTM"的 Y）：

   没有匹配，结果使用之前的

   | - | A | N | X | M | 
   ---------------------
   | A | 1   1   1   1 |
   | X | 1   1   2   2 |
   | Y | 1   1   2   2 |
   | T | 0   0   0   0 |
   | M | 0   0   0   0 |
  
  
第四次外循环（字符"AXYTM"的 T）：

   没有匹配，结果使用之前的

   | - | A | N | X | M | 
   ---------------------
   | A | 1   1   1   1 |
   | X | 1   1   2   2 |
   | Y | 1   1   2   2 |
   | T | 1   1   2   2 |
   | M | 0   0   0   0 |


第五次外循环（字符"AXYTM"的 M）：

   已经存在重合 A = A和X = X, 长度为2，内循环字符 "ANXM" 如果有匹配就累加1，存在 M = M

   | - | A | N | X | M | 
   ---------------------
   | A | 1   1   1   1 |
   | X | 1   1   2   2 |
   | Y | 1   1   2   2 |
   | T | 1   1   2   2 |
   | M | 1   1   2   3 |
   
   


最后的矩阵结果为：

[0, 0, 0, 0, 0]
[0, 1, 1, 1, 1]
[0, 1, 1, 2, 2]
[0, 1, 1, 2, 2]
[0, 1, 1, 2, 2]
[0, 1, 1, 2, 3]


*/
```

#### 解法：
```js
//递归写法
function lcs(stringA, stringB , m , n ) {
    if (m == 0 || n == 0) return 0;
    
    if (stringA[m-1] == stringB[n-1]) {
        return 1 + lcs(stringA, stringB, m-1, n-1);
    } else {
        // 字符错位比较
        return max(lcs(stringA, stringB, m, n-1), lcs(stringA, stringB, m-1, n));
    }
}
 
//迭代写法
function lcs2(a, b) {

    //初始化矩阵（横纵坐标每个元素都是 0 ）
    const matrix = Array(a.length + 1).fill().map(() => Array(b.length + 1).fill(0));

    for(let i = 1; i < a.length + 1; i++) {
        for(let j = 1; j < b.length + 1; j++) {
            if(a[i-1] === b[j-1]) {
                matrix[i][j] = 1 + matrix[i-1][j-1];
            } else {
                matrix[i][j] = Math.max(matrix[i-1][j], matrix[i][j-1]);
            }
        }
    }
    
    return matrix[a.length][b.length];
}


function max(a , b) {
    return (a > b) ? a : b;
}
 
 
 
const string1 = "AXYTM";
const string2 = "ANXM";
const m = string1.length;
const n = string2.length;
console.log( lcs(string1, string2, m, n) );  // 3 即 AXM
console.log( lcs2(string1, string2) );  // 3 即 AXM
```


**-->时间复杂度： O(2^n)**


### 动态规划(矩阵链相乘)
----


#### 问题描述

矩阵链相乘(Matrix Chain Multiplication)概念，n 行 m 列矩阵A和 m 行 p 列 矩阵B相乘，结果是 n 行 p 列的矩阵C。 如何获得最佳的矩阵链乘法划分和最少次数?

如A*B*C乘法，乘法满足结合律，所以 A\*(B\*C) = (A\*B)\*C

再如：((AB)C)D = ((A(BC))D) = (AB)(CD) = A((BC)D) = A(B(CD))




#### 关键：

矩阵的乘法的定义【i和j为下标, 中括号[]内的小写字母和数字也为下标】：

 - 若A=[a[ij]]是某个 m\*p矩阵，B=[b[ij]]树某个 p\*n矩阵，那么A和B的积是由所定义的一个 m\*n 矩阵 C=[c[ij]], 记作AB。
 - AB=(a[i1], a[i2], ..., a[ip]) * (b[1j], b[2j], ..., b[pj]) = a[i1]b[1j] + a[i2]b[2j] + ... + a[ip]b[pj]
 - 相乘后的结果是 c[ij]
 - 多个矩阵连续相乘构成"矩阵链"。


**例如：**

```js
设 A = [
  [2  3  -4]
  [1  2   3]
]

B = [
  [3   1]
  [-2  2]
  [5  -3]
]

那么

AB = [
  [(2)(3) + (3)(-2) + (-4)(5)    (2)(1) + (3)(2) + (-4)(-3)]
  [(1)(3) + (2)(-2) + (3)(5)      (1)(1) + (2)(2) + (3)(-3)]
] = [
  [-20  20]
  [14   -4]
]

其中：
A[11]=2, B[11]=3  =>  2*3
A[12]=3, B[21]=-2  =>  3*(-2)
A[13]=-4, B[31]=5  =>  (-4)*5
```


**由此看出 A[ab]A[bc]矩阵相乘的最小乘法次数为: a\*b\*c**


#### 解题思路(第一步)：

```base
例如给定一个数组 [10, 30, 5, 60]， 那么可以得出三个矩阵为：A => 10*30, B => 30*5, C => 5*60， 这三个矩阵的相乘方式有2种：

 - (AB)C = (10×30×5) + (10×5×60) = 1500 + 3000 = 4500 次
 - A(BC) = (30×5×60) + (10×30×60) = 9000 + 18000 = 27000 次
```

**计算(AB)C**

(AB)的乘法次数为 10×30×5 = 1500， (AB)相当于是10行5列，即10\*5，那么它和矩阵C相乘的次数为：10×5×60 = 3000


**计算 A(BC)**

(BC)的乘法次数为 30×5×60 = 9000，(BC)相当于是30行60列，即30\*60，那么它和矩阵A相乘的次数为：10×30×60 = 18000



#### 解题思路(第二步)：

两个矩阵相乘的次数算法, A是一个 n\*m 的矩阵，B是一个 m\*p 的矩阵，则乘积C为一个 n\*p 的矩阵：
```js
for (i=1;i<=n;i++) {
    
    for (j=1;j<=p;j++) { 
        
            c[i][j] = 0;
            
            for (k=1;k<=m;k++)  { 
                c[i][j] += a[i][k] * b[k][j];
            } 
            
     }
                     
}        
 ```

总共需要 n\*m\*p 的次数相乘


括号的分割算法，如

 - 如果不加括号： 10 - 7 - 3 = 0
 - 如果有括号： 10 - ( 7 - 3 ) = 6
 - 我们要实现一个括号相减的实现原理，输入 [10,7,3], 输出 6 才是想要的答案

```js
const nums = [10,7,3];
const parenthesesLength = 2; // 括号的长度(2个数字)

//不加括号的算法
//--------------
let res = nums.at(0); //获取数组的最大值
nums.forEach( (item,index) => {
    if (index > 0) {
        res = res - item;
    }
});

console.log(res); // 0 (不加括号)


//加括号的算法
//--------------
let resP = nums.at(0); //获取数组的最大值
nums.forEach( (item,index) => {
    if (index > 0) {
        
        //关键：循环括号的索引分割开来的数据，这里的括号起始索引为1
        const parenthesesIndex = nums.length - parenthesesLength; //括号的索引 (关键)

        if ( index > parenthesesIndex ) {
            let resDecrease = nums[parenthesesIndex];
            for (let k=parenthesesIndex+1; k < nums.length; k++) {
                resDecrease -= nums[k];
            }
            resP = resP - resDecrease;
        }
    }
});

console.log(resP); // 6
```
 
 

#### 解题思路(第三步)：
 
如果是多个矩阵相乘，就要考虑结合律。

我们可以使用 "穷举法"，列举出所有可能的计算次序，并计算每一种的相乘次数，找出最少的那种。

也可以使用 "动态规划" 的方法。i和j相当于输入的数组的索引，它们将表示分割出的矩阵的行和列的值的索引。将矩阵乘积简记为 A[i:j], i<=j。 考察计算 A[i:j] 的最优计算次序。这个计算次序在矩阵A[k-1]和A[k]之间将矩阵链断开，i < k <= j。

计算量：(A[i]A[i+1]...A[k-1])的计算量加上(A[k]A[k+1]...A[j])的计算量，再加上 A[i:k-1]和A[k:j]相乘的计算量。

用数组元素 c[i][j]来存储计算A[i:j]的最少乘法次数。




#### 解题思路(第四步)：

建立递归关系：

 - 设计算A[i:j]， 1 <= i <= j <= n， 所需要的最少数乘次数 C[i,j]，则原问题的最优值为C[1,n]
 - 当 i=j 时， A[i:j]=A[i]，因此，C[i,i]=0, i=1,2,...,n
 - 当 i<j 时，需要找到一个分割点k，在A[k]前断开：(A[i]...A[k-1])(A[k]...A[j]), 使C[i,j]达到最小

可以递归地定义 C[i,j]为：

 - p 为传入的数组, 它将自动分割成矩阵
 - 0  (i=j)
 - min(i < k <= j) { C[i,k-1] + C[k,j] + p[i]p[k]p[j+1] }    (i < j)

k的位置只有 j-i 种可能。

【注意：min的公式也是核心，它计算了给定括号顺序的乘法运算次数，并将值保存在辅助矩阵C中】



#### 解题思路(第五步)：
  
对于 1 <= i <= j <= n 不同的有序对(i,j)对应不同的子问题。因此，不同子问题的个数最多只有 n^2 个，由此可见，在递归计算时，许多子问题被重复计算多次。这也是该问题可以使用动态规划算法求解的一个显著特征。

"有序对"是离散数学中的一个概念，是对象a和b以指定次序，a先出现，b再出现的一个列举，记作(a,b)。


#### 算法步骤
```js
/*
过程：

数据：[10, 30, 5, 60]，  分割成三个矩阵为：A => 10*30, B => 30*5, C => 5*60
辅助矩阵（用来记忆结果，最后返回一个最小的值）：

[
  [0, 0, 0, 0],
  [0, 0, 0, 0],
  [0, 0, 0, 0],
  [0, 0, 0, 0]
]

结果：
[
    [0, 0, 0, 0],
    [0, 0, 1500, 4500],
    [0, 0, 0, 9000],
    [0, 0, 0, 0]
]

初始化：

matrix[1][1] = 0
matrix[2][2] = 0
matrix[3][3] = 0
matrix[4][4] = 0



第一次：chainLength=2, i < n - chainLength + 1 (即3) 时执行：

     matrix[1][2] = matrix[1][1] + matrix[2][2] + 10 * 30 * 5 = 1500 (parenthesesIndex=1)
     matrix[2][3] = matrix[2][2] + matrix[3][3] + 30 * 5 * 60 = 9000 (parenthesesIndex=2)


第二次：chainLength=3, i < n - chainLength + 1 (即2) 时执行：

     matrix[1][3] = matrix[1][1] + matrix[2][3] + 10 * 30 * 60 = 9000 + 18000 = 27000 (parenthesesIndex=1)
     matrix[1][3] = matrix[1][2] + matrix[3][3] + 10 * 5 * 60 = 1500 + 3000 = 4500 (parenthesesIndex=2)
     由于 27000 > 4500， 所以 matrix[1][3] = 4500


提示：i是起点，j是终点，相当于每次外循环，值 matrix[1][???] 被初始化一个最大值，剩下的 i 递增的结果都存储在矩阵的其他位置，让这些结果来对比 matrix[1][???]并重新赋予它更小的值。所以最终返回的最小值就是 matrix[1][???]



-------------------

如果数据为：[10, 30, 5, 60, 20]，则结果就为 8500，矩阵如下：

A => 10 x 30
B => 30 x 5
C => 5 x 60
D => 60 x 20
 

((AB)C)D = (10x30x5) + (10x5x60) + (10x60x20) = 1500 + 3000 + 12000 = 16500
(A(BC))D = (30x5x60) + (10x30x60) + (10x60x20) = 9000 + 18000 + 12000 = 39000
(AB)(CD) = (10x30x5) + (5x60x20) + (10x5x20) = 6000 + 1500 + 1000 = 8500
A((BC)D) = (30x5x60) + (30x60x20) + (30x20x10) = 9000 + 36000 + 6000 = 51000


[
  [0, 0, 0, 0, 0]
  [0, 0, 1500, 4500, 8500]
  [0, 0, 0, 9000, 9000]
  [0, 0, 0, 0, 6000]
  [0, 0, 0, 0, 0]
]


*/
```

#### 解法：
```js
// 递归写法（也可以使用记忆法和Math.min来得到最小值）
//--------------
// 矩阵 A[i] 具有维度 p[i-1] x p[i]  (i = 1..n)
function matrixChainOrder(p , i , j){
    if (i == j) return 0;
 
    let min = Number.MAX_SAFE_INTEGER;  //也可使用 MAX_VALUE

    // 在i与j之间使用k断开，相当于在不同的地方放置括号
    // 第一个和最后一个矩阵，递归计算
    // 每个括号的乘法计数
    for (let k = i; k < j; k++){
        const count = matrixChainOrder(p, i, k)  // (A[i]A[i+1]...A[k-1])的计算量
                    + matrixChainOrder(p, k + 1, j)  // (A[k]A[k+1]...A[j])的计算量
                    + p[i - 1] * p[k] * p[j];   // 分割出的两个矩阵的相乘次数计算
 
        if (count < min) min = count;
            
    }
 
    return min;
}
 
const arr = [10, 30, 5, 60];
const n = arr.length;
console.log( matrixChainOrder(arr, 1, n-1) );  // 4500



// 迭代写法
//--------------
function matrixChainOrder(p){
    const n = p.length; // 4
    const matrix = Array(n).fill(0).map(x => Array(n).fill(0)); // 使用 0 填充一个 4x4 的辅助矩阵，用来记忆结果
    let chainLength = 2; // 链的长度

    for (chainLength = 2; chainLength < n; chainLength++) {
        for (let i = 1; i < n - chainLength + 1; i++) { // i的起点变化
            const j = i + chainLength - 1;  // 不同终点
            matrix[i][j] = Number.MAX_SAFE_INTEGER;

            // A[i]A[i+1]...A[j] = A[i...j] 其中 A[i] 的维度是 p[i-1] x p[i]
            for (let parenthesesIndex = i; parenthesesIndex <= j - 1; parenthesesIndex++){ 

                const count = matrix[i][parenthesesIndex] 
                            + matrix[parenthesesIndex + 1][j]
                            + p[i - 1] * p[parenthesesIndex] * p[j];

                if (count < matrix[i][j]) {
                    matrix[i][j] = count;

                    //括号的分割方法(parenthesesIndex代表括号的起始索引)
                    console.log(parenthesesIndex); // 分别输出 1, 2
    

                }
                    
            }
        }

    }

    return matrix[1][n - 1];
}
```


**-->时间复杂度：  O(n^3)**

