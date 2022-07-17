# 常见算法基础(Level 2)

## 目录

 - [归并排序](#归并排序)
 - [快速排序](#快速排序)
 - [计数排序](#计数排序)
 - [桶排序](#桶排序)
 - [基数排序](#基数排序)
 - [堆排序](#堆排序)

### 归并排序
----

#### 概念

归并排序（Merge sort）是建立在归并操作上的一种有效的排序算法。该算法是采用分治法（Divide and Conquer）的一个非常典型的应用。

作为一种典型的分而治之思想的算法应用，归并排序的实现由两种方法：

- 自上而下的递归（所有递归的方法都可以用迭代重写，所以就有了第 2 种方法）；
- 自下而上的迭代；



#### 算法步骤

1.申请空间，使其大小为两个已经排序序列之和，该空间用来存放合并后的序列；

2.设定两个指针，最初位置分别为两个已经排序序列的起始位置；

3.比较两个指针所指向的元素，选择相对小的元素放入到合并空间，并移动指针到下一位置；

4.重复步骤 3 直到某一指针达到序列尾；

5.将另一序列剩下的所有元素直接复制到合并序列尾。


#### 过程：
```base
/*
过程：


              [5, 2, 4, 3, 1]
            [5, 2]     |     [4, 3, 1]
         [5]     [2]   |   [4]    [3, 1]
         [5]     [2]   |  [4]   [3]   [1]
    -------------------------------------
         [5]     [2]   |   [4]   [3]   [1]
           [2, 5]      |     [4]   [1, 3]
           [2, 5]      |       [1, 3, 4]
               [1, 2, 3, 4, 5]
               
               
               

递归顺序(子递归先执行)
├── 第3次：left=mergeSort([5,2]), right=mergeSort([4, 3, 1])  =>   [1,3,4]和[2,5]合并后为 [1,2,3,4,5]
│   ├── 第2_1次：left=mergeSort([5]), right=mergeSort([2])  =>   [2,5]
│   ├── 第2_2次：left=mergeSort([4]), right=mergeSort([3,1])  => [1,3,4] (合并后的结果)
│   │   ├── 第1次：left=mergeSort([3]), right=mergeSort([1])   =>   [1,3]
└──
 
      
*/              
```              
               

#### 解法：
```js
function mergeSort(arr) {  // 采用自上而下的递归方法
    var len = arr.length;
    if(len < 2) {
        return arr;
    }
    var middle = Math.floor(len / 2),
        left = arr.slice(0, middle),
        right = arr.slice(middle);

    /*
    也可写成：
    const left = arr.splice(0, middle);
    const right = arr;
    */

    return merge(mergeSort(left), mergeSort(right));
}



/*
也可写成：

function merge(left, right){
    var result = [];

    while (left.length && right.length) {
        if (left[0] <= right[0]) {
            result.push(left.shift());
        } else {
            result.push(right.shift());
        }
    }

    while (left.length) {
        result.push(left.shift());
    }
    while (right.length) {
        result.push(right.shift());
    }
        

    return result;
}
*/

/*
也可写成：

function merge(left, right){
    let i = 0;
    let j = 0;
    const result = [];

    while ( i < left.length && j < right.length ) {
        
        if ( left[i] < right[j] ) {
            result.push( left[i] );
            i++;
        } else {
            result.push( right[j] );
            j++;
        }

    }

    if ( i < left.length ) {
        return result.concat( left.slice(i) );
    } else {
        return result.concat( right.slice(j) );
    }
}
*/


function merge(left, right) {
    let arr = []

    // 如果数组中的任何一个为空，则跳出循环
    while (left.length && right.length) {

        // 在左右子数组的最小元素中选择较小的
        if (left[0] < right[0]) {
            arr.push(left.shift())  
        } else {
            arr.push(right.shift()) 
        }
    }
    
    // 连接剩余元素（如果没有遍历整个左侧或右侧数组）
    return [ ...arr, ...left, ...right ];
}

console.log( mergeSort([5, 2, 4, 3, 1]));  // [1, 2, 3, 4, 5]
```

**-->时间复杂度：O(n * log(n))**

### 快速排序
----

#### 分区过程：

1.先从数列中取出一个数作为基准数【主元(pivot)：也就是数组中间的那个值】

2.在数组的第一个元素处开始左指针, 从数组的最后一个元素开始右指针。

3.比较左指针元素，如果它"小于"pivot，则将左指针向"右"移动（将左索引 +1）。 继续此操作，直到左指针元素大于或等于pivot。

4.比较右指针元素，如果它"大于"pivot，则将右指针向"左"移动（将右索引 -1）。 继续此操作，直到右指针元素小于或等于pivot。

5.检查左指针是否小于或等于右指针元素，如果大于，则交换这两个元素。

6.完成第5步后，左右指针各移动一次。

7.如果左指针的索引仍然小于等于右指针的索引，则重复该过程(3/4/5/6步)； 否则返回左指针的索引。（注意是索引，不是值）


#### 过程：
```js
/*
过程：
----------------------------
原数组：[3, 5, 2, 4, 6, 1, 7]
中间数：4（这个数其实是随机的，这里为方便选择取中间）
[L]和[R]分别为左右指针。
↑ 表示已经排序的元素（每一次递归几轮后所有元素都已经排序完成，则进入下一次递归）。
▲ 表示基准值

初始指针：

[L]                     [R]
 ↓                       ↓
 3   5   2   4   6   1   7
             ▲



------------------------
初次递归 quickSort(myArr, 0, myArr.length - 1)，这里的left就是0，right就是5【基准值为4】：

            检查左指针，3 < 4, 左索引 +1

            [L]                     [R]
             ↓                       ↓
             3   5   2   4   6   1   7
                         ▲


            检查左指针，5 > 4, 停止移动

                [L]                 [R]
                 ↓                   ↓
             3   5   2   4   6   1   7
             ↑   ↑       ▲
             
             
 
            检查右指针，7 > 4,  右索引 -1

                [L]             [R]
                 ↓               ↓
             3   5   2   4   6   1   7
             ↑   ↑       ▲           ↑


            检查右指针，1 < 4,  停止移动

                [L]             [R]
                 ↓               ↓
             3   5   2   4   6   1   7
             ↑   ↑       ▲       ↑   ↑


            检查左右指针， 5 > 1, 则需要交换位置，让5调到1的右边，调整后
            
                [L]             [R]
                 ↓               ↓
             3   1   2   4   6   5   7
             ↑   ↑       ▲       ↑   ↑
             
             
             
             完成了左右指针比较，接着移动两个指针（左指针的索引仍然小于右指针的索引，则重复该过程）
              
                    [L]     [R]
                     ↓       ↓
             3   1   2   4   6   5   7
             ↑   ↑       ▲       ↑   ↑
             
             
             检查左指针，2 < 4, 左索引 +1
             
              
                        [L]  [R]
                         ↓   ↓
             3   1   2   4   6   5   7
             ↑   ↑   ↑   ▲       ↑   ↑ 
             
             
             检查左指针，4 = 4, 停止移动
             
              
                        [L]  [R]
                         ↓   ↓
             3   1   2   4   6   5   7
             ↑   ↑   ↑   ▲      ↑   ↑    
             
             
             
             检查右指针，6 > 4, 右索引 -1
             
                        [R]
                        [L]  
                         ↓
             3   1   2   4   6   5   7
             ↑   ↑   ↑   ▲  ↑   ↑   ↑    
                    
             
             检查右指针，4 = 4, 停止移动
             
                        [R]
                        [L]  
                         ↓
             3   1   2   4   6   5   7
             ↑   ↑   ↑   ▲  ↑   ↑   ↑       
             
             
             检查左右指针， 4 = 4, 则无需交换位置，调整后
             
             
                        [R]
                        [L]  
                         ↓
             3   1   2   4   6   5   7
             ↑   ↑   ↑   ▲  ↑   ↑   ↑
             
             
             完成了左右指针比较，接着移动两个指针（左指针的索引大于右指针的索引，则返回左指针的"索引" 4）
             
                        
                    [R]     [L]  
                     ↓       ↓
             3   1   2   4   6   5   7
             ↑   ↑   ↑   ▲  ↑   ↑   ↑ 
             
             
             
             递归第一次结束, 继续递归。
             
             【主元左侧的划分 (下面的递归第二、三、四次是步骤主元左侧的划分的详细过程)】
             由于 0 < index-1, 则quickSort(items, left, index - 1)， 索引left到index-1 （即索引0到3)，即[3,1,2,4]的递归
     

             【主元右侧的划分，省略了详细过程解说，同理参考左侧的划分过程】
             由于index < 5, 则quickSort(items, index, right)， 索引 index到right （即索引4到5)，即[5,6]的递归


           
------------------------
[3,1,2,4]递归 quickSort(items, left, index - 1)，这里的left就是0，right就是3【基准值为2】：


            [L]         [R]
             ↓           ↓
             3   1   2   4
                     ▲
                     
            检查左指针，3 > 2, 停止移动
                 
            [L]         [R]
             ↓           ↓
             3   1   2   4
             ↑       ▲   
                 

            检查右指针，4 > 2,  右索引 -1
 
            [L]     [R]
             ↓       ↓
             3   1   2   4
             ↑       ▲   ↑
             
            检查右指针，2 = 2, 停止移动
             
            [L]     [R]
             ↓       ↓
             3   1   2   4
             ↑       ▲   ↑ 
             
             检查左右指针， 3 > 2, 则需要交换位置，让3调到2的右边，调整后
             
            [L]     [R]
             ↓       ↓
             2   1   3   4
             ▲       ↑   ↑          
             
             
             完成了左右指针比较，接着移动两个指针（左指针的索引等于右指针的索引，则重复该过程）
             
                [R]
                [L]     
                 ↓       
             2   1   3   4
             ▲       ↑   ↑    
             
             检查左指针，1 < 2, 左索引 +1
             
             
                [R] [L]     
                 ↓   ↓   
             2   1   3   4
             ▲       ↑   ↑    
             
                     
             检查左指针，3 > 2, 停止移动
             
             
                [R] [L]     
                 ↓   ↓   
             2   1   3   4
             ▲       ↑   ↑    
             
             
             检查右指针，1 < 2,  停止移动
                
                [R] [L]     
                 ↓   ↓   
             2   1   3   4
             ▲   ↑   ↑   ↑    
                
                     
             检查左右指针， 3 > 1, 则需要交换位置，让3调到1的右边，调整后
             
                [R] [L]     
                 ↓   ↓   
             2   3   1   4
             ▲   ↑   ↑   ↑     
             
             
             完成了左右指针比较，接着移动两个指针（左指针的索引大于右指针的索引，则返回左指针的"索引" 3）
             
            [R]         [L]     
             ↓           ↓   
             2   3   1   4
             ▲   ↑   ↑   ↑     
              
             递归第二次结束，接下去递归，由于 0 < index-1, 则quickSort(items, left, index - 1)， 索引left到index-1 （即索引0到2), [2,3,1]的递归
             
             
             
------------------------               
[2,3,1]递归 quickSort(items, left, index - 1)，这里的left就是0，right就是2【基准值为3】：       


            [L]     [R]
             ↓       ↓
             2   3   1 
                 ▲    

            检查左指针，2 < 3, 左索引 +1

                [L] [R]
                 ↓   ↓
             2   3   1 
             ↑   ▲         
             
             
            检查左指针，3 = 3,  停止移动
             
                [L] [R]
                 ↓   ↓
             2   3   1 
             ↑   ▲     
             
             检查右指针， 1 < 3,  停止移动
             
                [L] [R]
                 ↓   ↓
             2   3   1 
             ↑   ▲   ↑  
             
              
                         
             检查左右指针， 3 > 1, 则需要交换位置，让3调到1的右边，调整后
                         
                [L] [R]
                 ↓   ↓
             2   1   3 
             ↑   ↑   ▲  
                                
             
            完成了左右指针比较，接着移动两个指针（左指针的索引大于右指针的索引，则返回左指针的"索引" 2）
                     
                [R] [L] 
                 ↓   ↓
             2   1   3 
             ↑   ↑   ▲          
             
             
             递归第三次结束，接下去递归，由于 0 < index - 1, 则quickSort(items, left, index - 1)， 索引left到index-1 （即索引0到1), [2,1]的递归
             
             
             
             
             
------------------------               
[2,1]递归 quickSort(items, left, index - 1)，这里的left就是0，right就是1【基准值为2】：         


            检查左指针，2 = 2, 停止移动

            [L]  [R]
             ↓   ↓
             2   1 
             ▲               
             
            检查右指针，1 < 2,  停止移动
            
            [L]  [R]
             ↓   ↓
             2   1 
             ▲   ↑           
                  
                  
            检查左右指针， 2 > 1, 则需要交换位置，让2调到1的右边，调整后
            
            [L]  [R]
             ↓   ↓
             1   2 
             ↑   ▲        
                      
            
            完成了左右指针比较，接着移动两个指针（左指针的索引大于右指针的索引，则返回左指针的"索引" 1）
            
            [R]  [L]
             ↓   ↓
             1   2
             ↑   ▲            
            
            
             递归第四次结束，由于不满足left < index-1 和 index < right, 就不用再继续划分了。
               
             
     
------------------------               
[3,1,2,4]递归全部完成后的排序为：         
 
           1   2   3   4   6   5   7
           
           
[5,6]递归的过程同理。

          
            
*/
```


#### 解法：
```js
function swap(items, leftIndex, rightIndex){
    var temp = items[leftIndex];
    items[leftIndex] = items[rightIndex];
    items[rightIndex] = temp;
}


function partition(items, left, right) {
    var pivot   = items[Math.floor((right + left) / 2)], 
        i       = left, //左指针索引
        j       = right; //右指针索引
        
    while (i <= j) {
        while (items[i] < pivot) {
            i++;
        }
        while (items[j] > pivot) {
            j--;
        }
        if (i <= j) {
            swap(items, i, j);
            i++;
            j--;
        }
    }
    return i;
}


function quickSort(items, left, right) {
    var index;
    if (items.length > 1) {
        index = partition(items, left, right); //从分区返回的索引
        if (left < index - 1) { //主元“左侧”的更多元素
            quickSort(items, left, index - 1);
        }
        if (index < right) { //主元“右侧”的更多元素
            quickSort(items, index, right);
        }
    }
    return items;
}



var myArr = [3, 5, 2, 4, 6, 1, 7];
var sortedArray = quickSort(myArr, 0, myArr.length - 1);
console.log(sortedArray); // [1, 2, 3, 4, 5, 6, 7]
```

**-->时间复杂度：O(n * log(n))**

### 计数排序
----

#### 概念

它是一种分布式排序，分布式排序使用已组织好的辅助数据结构(称为**桶**)，然后进行合并，得到排好序的数组。

它是一种非比较类排序 它的时间复杂度为O(n+k), k是最大值与最小值的差值；计数排序快于任何一种比较排序算法，但是它为此牺牲了空间。

1.以数组元素值为键，出现次数为值存进一个临时数组，最后再遍历这个临时数组还原回原数组。

2.因为 JavaScript 的数组下标是以字符串形式存储的，所以计数排序可以用来排列负数，但不可以排列小数


#### 算法步骤
```js
/*
过程：
----------------------------
原数据：[5, 2, 7, 3, 1, 3]

第一步： 使用最大的数值创建计数(使用原数组作为下标，所以不支持小数排序)

        new Array(7+1)  =>  [empty × 8], length为8， 元素取下标相当于undefined
        

第二步： 原数组的值，对应到空数组的下标后为（如果有重复值，则计数会+1）：
    
        [empty, 1, 1, 2, empty, 1, empty, 1]
    
    
第三步： 原数组的顺序下标和计数的下标对比关系：
    
    
                           
                              5  2  7  3  1  3
        原数组的顺序下标：       ↓  ↓  ↓  ↓  ↓  ↓ 
                              0  1  2  3  4  5
        计数的i(累加的新值)：    ↓  ↓  ↓  ↓  ↓  ↓
                              1  2  3  3  5  7

        （因为存在empty，每次empty都累加1，所以累加的i就可以对应到元数组的值上）
 
             
*/
```

#### 解法：
```js
function countingSort(nums) {
    let max = Math.max(...nums); // 最大值，也可以写成 Math.max.apply(null,nums)

    // 装桶
    //------------
    let counts = new Array(max+1); 

    // 把传入数组的值作为下标,装入一个长度为数组最大值长度的临时数组
    nums.forEach(element => {
        if ( counts[element] === undefined ) counts[element] = 0; //元素位置一开始的初始化,如果有重复的元素，则计数器会增加1 
        counts[element] = counts[element] + 1;
    });

    console.log(counts); // [empty, 1, 1, 2, empty, 1, empty, 1]
    

    // 还原原数组
    //------------
    let sortedIndex = 0;  // 定义一个索引,这个索引是后面用来改变原数组的 
    counts.forEach( (currentCount, i) => {
        while (currentCount > 0) {
            nums[sortedIndex++] = i; // 就把原数组的第index位赋值为 i（这个i就相当于新的值）
            currentCount--; // 每赋值完一次后 临时数组当前位的值就--   如果=0 就说明这位上没有值了
        }
    });


    return nums;
}

console.log( countingSort([5, 2, 7, 3, 1, 3]));  // [1, 2, 3, 3, 5, 7]
```

找数组最大值，还可以使用函数：
```js
function findMaxValue(arr){
    let max = arr[0];
    for (let i = 1; i < arr.length; i++) {
        if (arr[i] > max) {
            max = arr[i];
        }
    }
    return max;
}
```

**-->时间复杂度：O(n+k)**

### 桶排序
----

#### 概念

也称为"**箱排序**"，也是分布式排序发算法，也是一种分而治之算法。

1. 计算每个桶中需要分布的元素个数（公式：最大值和最小值的差值除以桶的大小）
2. 迭代原数组，并将元素正确插入到桶中，使得每个桶内的值成组有规律递增
2. 对每个桶内的元素进行排序
4. 对所有的桶的元素进行合并



#### 算法步骤
```js
/*
过程：
----------------------------
原数据：[5, 2, 7, 3, 1, 8]

第一步： 根据想要的桶的大小，计算桶的数量，我们这里希望每个桶装3个元素， (8-1)/3 + 1 约等于3个桶
        
第二步： 将每个桶中放入正确的元素
    
        [2, 3, 1]  [5]  [7,8]
    
    
第三步： 分别对3个桶内的元素排序

        [1, 2, 3]  [5]  [7,8]
    
第四步： 按顺序合并每个桶

       [1, 2, 3, 5, 7, 8]
             
*/
```

#### 解法：
```js
function bucketSort(arr) {
    const buckets = createBuckets(arr, 3); //桶的大小设为3(每个桶装3个元素)
    const sortedArr = [];
    
    //分别给每个桶的元素排序(使用合适的排序法即可)，最后按顺序合并每个桶
    for(let i = 0; i < buckets.length; i++) {
        buckets[i].sort((a,b) => {
 		  return a-b
	    });  //不要直接写sort()，否则个位数和十位数将不能正常排序，可以使用其他算法排序， sort()方法会改变原数组

        sortedArr.push( ...buckets[i] );

        /* 也可以写成：
        for( let j = 0; j < buckets[i].length; j++) {
            sortedArr.push(buckets[i][j]);
        }
        */
    }

    return sortedArr;
}


//创建桶
function createBuckets(arr, bucketSize) {
    const max = Math.max(...arr);
    const min = Math.min(...arr);
    const bucketCount = Math.floor((max - min) / bucketSize) + 1; // 计算每个桶中需要分布的元素个数

    const buckets = [];
    //初始化每个桶为数组格式
    for(let i = 0;i < bucketCount; i++) {
        buckets[i] = [];
    }

    //迭代原数组，根据数组的值的大小，计算每个数组应该放置到哪一个桶中
    for(let i = 0;i < arr.length; i++) {
       const bucketIndex = Math.floor( (arr[i] - min) / bucketSize );
       buckets[bucketIndex].push( arr[i] );
    }

    return buckets;
}


console.log( bucketSort([5, 2, 7, 3, 1, 8]) );  //  [1, 2, 3, 5, 7, 8]
```

**-->时间复杂度：O(n+k)**

### 基数排序
----

#### 概念

根据数字的有效位(个位数、十位数、百位数、千位数)或基数将整数分布到桶中。基数是基于数组中的值的记数制的。例如对于十进制数，使用基数10，相当于10个桶来装。

它也是一种分布式排序。可以从低位到高位排，或者高位到低位排，下面是低位到高位

1. 将所有代比较数值（正整数）统一为同样的数位长度，数位较短的数前面补零
2. 对于十进制数字，使用基数为10，即使用10个桶来分布元素
3. 先排每个数"个位"数字[0-9]，根据数字放到不同桶中
4. 将桶连接，把数字按照第一次的顺序取出来
5. 继续排每个数的"十位"数字[0-9]，根据数字放到不同桶中
6. 将桶连接，把数字按照第二次的顺序取出来。 
7. 依次类推，根据列表中最大数字位数循环。


#### 算法步骤
```js
/*
过程：
----------------------------
原数据：[5, 2, 17, 3, 21, 8]

第一步： 先排个位数字[0-9]，根据数字对应桶的索引，分别放到不同桶中
【注意：也可以使用计数排序的方式计算bucketIndex，它虽然导致桶的元素不同， 但是依然能够还原排序】
    
         
            21   2   3       5      17   8
        __  __  __  __  __  __  __  __  __  __ 
         0   1   2   3   4   5   6   7   8   9
         
         
    
第二步： 将桶连接，把数字按照第一次的顺序取出来

        21   2   3   5   17   8
        
        
    
第三步： 继续排十位数字[0-9]，根据数字对应桶的索引，分别放到不同桶中

 
        8  
        5
        3
        2   17  21
        __  __  __  __  __  __  __  __  __  __ 
         0   1   2   3   4   5   6   7   8   9
         
    
第四步： 将桶连接，把数字按照第二次的顺序取出来

        2  3  5  8  17  21
         
         
             
*/
```


#### 解法：
```js
function radixSort(array) {
    const {length} = array;
    if (!Array.isArray(array) || length <= 1) return;


    const buckets = [];
    const max = Math.max(...array);
    const min = Math.min(...array);
    let significantDigit = 1;  //有效位(个位数、十位数、百位数、千位数)，默认是个位数

    // 确定最大值的位数（转化为字符串后的长度），也可以写成 (max+'').length
    significantDigit = String(max).length; 


    // 初始化桶（10进制就使用10个桶）
    //------------
    for (let i = 0; i < 10; i++) {
        buckets[i] = [];
    }

    //迭代有效位（外循环）
    //------------
    for (let i = 0; i < significantDigit; i++) {

        for (let j = 0; j < length; j++) {
            const str = String(array[j]); 

            if (str.length >= i+1) {
                // 获取当前位的值，作为插入的索引
                const bucketIndex = parseInt(str[str.length - 1 - i]); 
            
                /*
                // 注意：也可以使用计数排序的方式计算bucketIndex，它虽然导致桶的元素不同， 但是依然能够还原排序
                const bucketIndex = Math.floor((array[j] - min)/significantDigit) % 10;
                */

                buckets[bucketIndex].push(array[j]);
            } else {
                // 处理位数不够的情况，高位默认为 0
                buckets[0].push(array[j]);
            }
        }

        // 清空旧的数组
        array.splice(0, length); 


        // 使用桶重新初始化数组(还原)
        for(let i = 0; i < buckets.length; i++) {
            array.push( ...buckets[i] );
            buckets[i] = [];
        }
        
    }

    return array;
}


console.log( radixSort([5, 2, 17, 3, 21, 8]) );  //  [2, 3, 5, 8, 17, 21]
```


**-->时间复杂度：O(nk)**


### 堆排序
----

#### 概念

堆是一种特殊的二叉树

- 二叉搜索树特点：左侧节点小于父节点，右侧节点大于父节点
- 二叉堆特点：每个父结点的值都大于或等于其左右子结点的值，称为大顶堆；或者每个父结点的值都小于或等于其左右子结点的值，称为小顶堆。

二叉树有两种表示形式：指针和数组(使用索引)

#### 算法步骤
```js
/*
比如数字通常存储为 1 2 3 4 5 6 7
变成堆的数据结构则为（注意索引顺序是0，1，2，3，4，5，6排列）：
注意：堆的索引都是不变的，按顺序从二叉树由上至下。
下面的二叉树使用索引来检索位置

                       1(0)
                      /     \
                   2(1)      3(2)
                 /    \      /   \
                4(3) 5(4)  6(5)  7(6)
                
                
                
       
--------------------          
默认数据： [5,3,1,7,6,4,2]

转换为最大堆过程：


--------------------   
递归初次 heapify([5,3,1,7,6,4,2], 3(i), 7(heapSize))

                     5
                   /   \
                  3     1
                 / \    / \   
                7   6  4   2     
                
                
第1次堆化，i=3, heapSize=7, 当前值: array[3]=7；left和right不存在

第2次堆化，i=2, heapSize=7, 当前值: array[2]=1；left=array[5]=4，right=array[6]=2

    值 4>1, 父节点(最大的)索引 = 5
    值 2<4, 不赋值
    索引 5(largest) != 2(index), 交换它们,结果如下：
    
                     5
                   /   \
                  3      4
                 / \    / \   
                7   6  1   2   
    
              [5,3,4,7,6,1,2]
    
   新增递归 heapify([5,3,4,7,6,1,2], 5(largest), 7(heapSize))
    
   第2_1次堆化，i=5, heapSize=7, 当前值: array[5]=1；left和right不存在 
    
   第2_2次堆化，i=4, heapSize=7, 当前值: array[4]=6；left和right不存在
   
   第2_3次堆化，i=3, heapSize=7, 当前值: array[3]=7；left和right不存在
    
   第2_4次堆化，i=2, heapSize=7, 当前值: array[2]=4；left=array[5]=1，right=array[6]=2, 由于left和right都小于4，且索引2(i)=2(largest)，无交换结果不变
   
   第2_5次堆化，i=1, heapSize=7, 当前值: array[1]=3；left=array[3]=7，right=array[4]=6    
   
       值 7>3, 父节点(最大的)索引 = 3
       值 6<7, 不赋值
       索引 3(largest) != 1(index), 交换它们,结果如下：         
               
               
                           5
                         /   \
                        7     4
                       / \    / \   
                      3   6  1   2   
    
                    [5,7,4,3,6,1,2]
    
       新增递归 heapify([5,7,4,3,6,1,2], 3(largest), 7(heapSize))
   
       第2_5_1次堆化，i=3, heapSize=7, 当前值: array[3]=3；left和right不存在
    
       第2_5_2次堆化，i=2, heapSize=7, 当前值: array[2]=4；left=array[5]=1，right=array[6]=2, 由于left和right都小于4，且索引2(i)=2(largest)，无交换结果不变
       
       第2_5_3次堆化，i=1, heapSize=7, 当前值: array[1]=7；left=array[3]=3，right=array[4]=6, 由于left和right都小于7，且索引1(i)=1(largest)，无交换结果不变
       
       第2_5_4次堆化，i=0, heapSize=7, 当前值: array[0]=5；left=array[1]=7，right=array[2]=4     
     
           值 7>5, 父节点(最大的)索引 = 1
           值 4<7, 不赋值
           索引 1(largest) != 0(index), 交换它们,结果如下：         
                        
               
                                 7
                               /   \
                              5     4
                             / \    / \   
                            3   6  1   2   
    
                         [7,5,4,3,6,1,2]
             
           新增递归 heapify([7,5,4,3,6,1,2], 1(largest), 7(heapSize))          
     
           第2_5_4_1次堆化，i=1, heapSize=7, 当前值: array[1]=5；left=array[3]=3，right=array[4]=6
           
     
               值 3<5, 不赋值
               值 6>5, 父节点(最大的)索引 = 4
               索引 4(largest) != 1(index), 交换它们,结果如下： 
               
                                        7
                                      /   \
                                     6     4
                                    / \    / \   
                                   3   5  1   2   
    
                                [7,6,4,3,5,1,2]    

               新增递归 heapify([7,6,4,3,5,1,2], 4(largest), 7(heapSize)) 【此次递归从i=4 ~ i=0都不交换，保持原结果】  
                    
                    
           第2_5_4_2次堆化，i=0, heapSize=7, 当前值: array[0]=7；left=array[1]=6，right=array[2]=4, 由于left和right都小于7，且索引0(i)=0(largest)，无交换结果不变
 

               
第3次堆化(在第2_5_4_2次堆化之后)，i=1, heapSize=7, 当前值: array[1]=6；left=array[3]=3，right=array[4]=5, 由于left和right都小于6，且索引1(i)=1(largest)，无交换结果不变

第4次堆化(在第2_5_4_2次堆化之后)，i=0, heapSize=7, 当前值: array[0]=7；left=array[1]=6，right=array[2]=4, 由于left和right都小于7，且索引0(i)=0(largest)，无交换结果不变
                        
 
         
--------------------   
最终排序需要使用最大堆 [7,6,4,3,5,1,2]   


                     7
                   /   \
                  6     4
                 / \    / \   
                3   5  1   2
                
排序过程（heapSize逐渐递减）：
【每次交换索引 0 和 X 后，都使用heapify()函数将根节点变成最大】

第一次： 交换索引 0 和 heapSize-1=6


                     2
                   /   \
                  6     4
                 / \    / \   
                3   5  1   7
                
              [2,6,4,3,5,1,7]

        下移根节点 heapify([2,6,4,3,5,1,7], 0, 6)
        
        堆化后的结果 
        
                     6
                   /   \
                  5     4
                 / \    / \   
                3   2  1   7
                
              [6,5,4,3,2,1,7]
        
        


第二次： 交换索引 0 和 heapSize-2=5


                     1
                   /   \
                  5     4
                 / \    / \   
                3   2  6   7
                
                [1,5,4,3,2,6,7]

        下移根节点 heapify([1,5,4,3,2,6,7], 0, 5)

        堆化后的结果 
        
                     5
                   /   \
                  3     4
                 / \    / \   
                1   2  6   7
                
              [5,3,4,1,2,6,7]
        
        


第三次： 交换索引 0 和 heapSize-3=4


                     2
                   /   \
                  3     4
                 / \    / \   
                1   5  6   7
                
              [2,3,4,1,5,6,7]
              

        下移根节点 heapify([2,3,4,1,5,6,7], 0, 4)

        堆化后的结果 
        
                     4
                   /   \
                  3     2
                 / \    / \   
                1   5  6   7
                
              [4,3,2,1,5,6,7]
        
        

第四次： 交换索引 0 和 heapSize-4=3, 过程省略...   堆化后的结果 [3,1,2,4,5,6,7]

第五次： 交换索引 0 和 heapSize-5=2, 过程省略...   堆化后的结果 [1,2,3,4,5,6,7]

第六次： 交换索引 0 和 heapSize-6=1，结束

*/
```

#### 解法：
```js
function swap(array, a, b) {
  [array[a], array[b]] = [array[b], array[a]];
}

//堆化
function heapify(array, index, heapSize) {
  let largest = index;  //父节点(最大的) ，如果index为0，则为根节点
  const left = (2 * index) + 1;  // 左侧子节点
  const right = (2 * index) + 2;  // 右侧子节点
  if (left < heapSize && array[left] > array[index]) { // 如果左侧子节点 > 父节点，则将父节点(最大的)赋值成左侧子节点
    largest = left;
  }
  if (right < heapSize && array[right] > array[largest]) {  // 如果右侧子节点 > 父节点(最大的)，则将父节点(最大的)赋值成右侧子节点
    largest = right;
  }
  if (largest !== index) {
    swap(array, index, largest);  //交换当前索引节点和父节点(最大的)
    heapify(array, largest, heapSize);
  }
}

function buildMaxHeap(array) {
  for (let i = Math.floor(array.length / 2); i >= 0; i -= 1) {  //父节点的索引相当于：Math.floor((index-1)/2) 
    heapify(array, i, array.length);
  }
  return array;
}


//堆排序算法
function heapSort(array) {
  let heapSize = array.length;
  buildMaxHeap(array);  //步骤1：用数组创建一个最大堆用作源数据  [7, 6, 4, 3, 5, 1, 2]
  while (heapSize > 1) {
    swap(array, 0, --heapSize);  //步骤2：创建最大堆后，最大值会被存储在堆的第一个位置。要将它替换为堆的最后一个值，将堆的大小减1
    heapify(array, 0, heapSize); //步骤3：将堆的根节点下移并重复步骤2，直到堆的大小为1
  }
  return array;
}

console.log( heapSort([5,3,1,7,6,4,2]) ); // [1, 2, 3, 4, 5, 6, 7]

```

**-->时间复杂度：O(n * log(n))**



