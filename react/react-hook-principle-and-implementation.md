# React Hook的原理及其实现(useState, useEffect, useRef, useMemo, useCallback)


## 目录

 - [useState()实现方法1](#useState实现方法1)
 - [useState()实现方法2](#useState实现方法2)
 - [useEffect()实现](#useEffect实现)
 - [useRef()实现](#useRef实现)
 - [useMemo()实现](#useMemo实现)

 
### 基础
(1)ES6数组解构：
```js
const [count, setCount] = [1, 2];
```
相当于 
```js
var count = 1, setCount = 2;
```
(2)闭包的相关知识原理


### useState()实现方法1

```js
function useState(initialValue) {
    let currentVal = initialValue;

    // state是是一个内部闭包(closure)函数
    function state() {
        return currentVal;
    }

    // 在 useState 的范围内设置 currentVal
    function setState(newVal) {
        currentVal = newVal;
    }
    
    // 注意，不能直接使用 state()里面的内容作为结果返回，否则将导致setState的结果无效
    // 当我们从 useState 的输出中解构 count 或者 count2 时，它引用了初始 useState 调用时的 currentVal, 且再也不会改变了
    return [state, setState];
}

function App() {
    const [count, setCount] = useState(111);
    const [count2, setCount2] = useState(222);
    console.log( count() );  // 111
    console.log( count2() );  // 222

    setCount(count() + 1);
    setCount2(count2() + 1);

    console.log( count() );  // 112
    console.log( count2() );  // 223

}

App();
```

### useState()实现方法2

与React一样，它跟踪组件状态（在我们的示例中，它只跟踪一个组件，状态位于 currentVal 中）

这种设计允许 CustomReact “渲染”您的函数组件，这允许它每次使用正确的闭包分配内部 currentVal 值

```js
const CustomReact = (function () {
    let currentVal; // 在模块范围内保持我们的状态
    return {
        render(Component) {
            const currentComponent = Component();
            currentComponent.render(); //执行组件内的render()函数
            return Component();
        },
        useState(initialValue) {
            currentVal = currentVal || initialValue; // 每次运行重新分配
            function setState(newVal) {
                currentVal = newVal;
            }
            return [currentVal, setState];
        }
    }
})();


function Counter() {
    const [count, setCount] = CustomReact.useState(333);

    return {
        increment: () => setCount(count + 1),
        render: () => console.log('render:', count)
    }

}

let MyApp = CustomReact.render(Counter);  // render: 333
MyApp.increment();
MyApp = CustomReact.render(Counter);  // render: 334
```

### useEffect()实现

与 setState 不同，useEffect 是异步执行的，这意味着遇到闭包问题的机会更多。

```js
const CustomReact2 = (function () {
    let currentVal; // 在模块范围内保持我们的状态
    let lastDependencies; //触发过的最后依赖项
    return {
        render(Component) {
            const currentComponent = Component();
            currentComponent.render(); //执行组件内的render()函数
            return Component();
        },
        useEffect(callback, depArray) {
            const hasNoDeps = !depArray; // 如果存在数组，则转化为 false， 留空则为true

            // every()只要有一个不满足就返回false；返回false可跳出循环
            const hasChangedDeps = lastDependencies ? !depArray.every((el, i) => el === lastDependencies[i]) : true;
            if (hasNoDeps || hasChangedDeps) {
                callback();
                
                //重新赋值lastDependencies。来判断依数数组内的项目是否有变化
                lastDependencies = depArray;
            }
        },
        useState(initialValue) {
            currentVal = currentVal || initialValue; // 每次运行重新分配
            function setState(newVal) {
                currentVal = newVal;
            }
            return [currentVal, setState];
        }
    }
})();

function Counter2() {
    const [count, setCount] = CustomReact2.useState(444);

    CustomReact2.useEffect(() => {
        console.log('effect:', count);
    }, [count]);

    return {
        increment: () => setCount(count + 1),
        noChange: () => setCount(count),
        render: () => console.log('render:', count)
    }

}

let MyApp2 = CustomReact2.render(Counter2);  
// effect: 444
// render: 444
MyApp2.increment();
MyApp2 = CustomReact2.render(Counter2);  
// effect: 445
// render: 445
MyApp2.noChange();
MyApp2 = CustomReact2.render(Counter2);  
// 没有触发effect
// render: 445


/*
如果useEffect不写第二个参数，则输出（触发3次effect）：

// effect: 444
// render: 444
// effect: 444
--
// effect: 445
// render: 445
// effect: 445
--
// effect: 445
// render: 445
// effect: 445
*/


/*
如果useEffect第二个参数为空数组 []，则输出：

// effect: 444
// render: 444
--
// 没有触发effect
// render: 445
--
// 没有触发effect
// render: 445

*/
```


### useRef()实现

ref 与 state 非常相似，因为它在渲染之间持续存在。 然而，关键区别在于 ref 的更改不会导致您的组件重新渲染。
```js
useRef() 
```
相当于 
```js
useState({current: initialValue })[0]
```


### useMemo()实现

useCallback()和它类似，二者的区别是useMemo返回缓存的变量，useCallback返回缓存的函数。即：
```js
useCallback(fn, deps) === useMemo(() => fn, deps)
```

```js
function useMemo(callback, depArray) {
    
    let lastDependencies; //触发过的最后依赖项

    // getLastDependencies是是一个内部闭包(closure)函数
    function getLastDependencies() {
        return lastDependencies;
    }

    const hasNoDeps = !depArray;
    // every()只要有一个不满足就返回false；返回false可跳出循环
    const hasChangedDeps = getLastDependencies() ? !depArray.every((el, i) => el === getLastDependencies()[i]) : true;
    if (hasNoDeps || hasChangedDeps) {

        //重新赋值lastDependencies。来判断依数数组内的项目是否有变化
        lastDependencies = depArray;

        return callback();

    }

}



function AppMemo() {

    const [clickTimeCount, setClickTimeCount] = useState(999);
	const timeOption = useMemo(() => {
        const val = clickTimeCount();
	    return { val }
	}, [clickTimeCount()])  //表示更新依赖于 clickTimeCount() 的变化

    console.log(timeOption);   // {val: 999}


}

AppMemo();
```