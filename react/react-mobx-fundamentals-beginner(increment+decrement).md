# react-mobx-fundamentals-beginner(increment+decrement)

## File Structures

```sh
src/
├── index.js (入口文件，用于客户端/浏览器)
├── App-simple.js (React组件-无状态函数版本)
└── App.js (React组件)
```


## Installation And Test


**Step 1.** First, using an absolute path into your app folder directory.

```sh
$ cd /{your_directory}/react-mobx-fundamentals-beginner\(increment+decrement\)
```


**Step 2.** Before doing all dev stuff make sure you have `Node 14+` installed. After that, run the following code in the main directory to install the node module dependencies.

```sh
$ sudo npm install
```

**Step 3.** Run this project with `create-react-app`

```sh
$ npm run start
```

**Step 4.** When you done, this will spin up a server that can be accessed at

```sh
http://localhost:3000
```





---

### index.js

不要把 computed 和 autorun 搞混。它们都是响应式调用的表达式，但是，如果你想响应式的产生一个可以被其它 observer 使用的值，请使用 @computed，如果你不想产生一个新值，而想要达到一个效果，请使用 autorun。 举例来说，效果是像打印日志、发起网络请求等这样命令式的副作用。

```js
import React from 'react';
import ReactDOM from 'react-dom';

import App from './App';


//store
import { makeObservable, observable, computed, action } from 'mobx';

class oneStore {
  count = 0;

  constructor() {
    makeObservable(this, {
      count: observable,
      isNegative: computed,
      increase: action,
      decrease: action
    });
  }

  get isNegative() {
    return this.count < 0 ? 'Yes' : 'No';
  }

  increase() {
    this.count += 1;
  }

  decrease() {
    this.count -= 1;
  }
}


//
const store = new oneStore();


ReactDOM.render(
  <React.StrictMode>
      <App myCounter={store}/>
  </React.StrictMode>,
  document.getElementById('root')
)

```

装饰器语法可以写成：
```js
import {observable, computed} from "mobx";
class oneStore {
    @observable count = 0;

    constructor(count) {
        this.count = count;
    }

    @computed get isNegative() {
        return this.count < 0 ? 'Yes' : 'No'；
    }
}
```

也可以使用 decorate 来引入:
```js
import {decorate, observable, computed} from "mobx";
class oneStore {
    count = 0;
    constructor(count) {
        this.count = count;
    }

    get isNegative() {
        return this.count < 0 ? 'Yes' : 'No'；
    }
}

decorate(oneStore, {
    count: observable,
    isNegative: computed
})
```



### App.js

observer 函数/装饰器可以用来将 React 组件转变成响应式组件。 它用 mobx.autorun 包装了组件的 render 函数以确保任何组件渲染中使用的数据变化时都可以强制刷新组件。 observer 是由单独的 mobx-react 包提供的。


```js
import React, { Component } from 'react';
import { observer } from 'mobx-react';

class App extends Component {
	render() {
		const myCounter = this.props.myCounter;
		return (
			<React.Fragment>
				Count: {myCounter.count}  <hr />

				Is negative? {myCounter.isNegative}

				<hr />

				<button onClick={myCounter.increase.bind(myCounter)}>Add</button>
				<button onClick={myCounter.decrease.bind(myCounter)}>Subtract</button>
			</React.Fragment>

		);
	}
}
observer(App);

export default observer(App);
```

装饰器语法可以写成：
```js
import React, { Component } from 'react';
import { observer } from 'mobx-react';

@observer
class App extends Component {
	render() {
		const myCounter = this.props.myCounter;
		return (
			<React.Fragment>
				Count: {myCounter.count}  <hr />

				Is negative? {myCounter.isNegative}

				<hr />

				<button onClick={myCounter.increase.bind(myCounter)}>Add</button>
				<button onClick={myCounter.decrease.bind(myCounter)}>Subtract</button>
			</React.Fragment>

		);
	}
}
export default App;
```

### App-simple.js (无状态函数版本)

```js
import React from 'react';
import { observer } from 'mobx-react';

const App = observer( (props) => {

	const {myCounter} = props;
	return (
		<React.Fragment>
		  Count: {myCounter.count}  <hr />
	  
		  Is negative? {myCounter.isNegative}
	  
		  <hr />
	  
		  <button onClick={myCounter.increase.bind(myCounter)}>Add</button>
		  <button onClick={myCounter.decrease.bind(myCounter)}>Subtract</button>
		</React.Fragment>
	  )
});

export default App;

```
