# react-redux-fundamentals-beginner(increment+decrement)

## File Structures

```sh
src/
├── reducers.js
├── index.js (入口文件，用于客户端/浏览器)
├── App-simple.js (React组件-无状态函数版本)
├── App-hook.js (React组件-Hook版本)
└── App.js (React组件)
```


## Installation And Test


**Step 1.** First, using an absolute path into your app folder directory.

```sh
$ cd /{your_directory}/react-redux-fundamentals-beginner\(increment+decrement\)
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
```js
import React from 'react'
import ReactDOM from 'react-dom'
import { Provider } from 'react-redux'

import App from './App';

//store
import { createStore } from 'redux';

import countReducer from './reducers.js'

/*
Output: 
{
    count: 0
}
*/

const store = createStore(countReducer);

// Whenever the store state changes, update the UI by
// reading the latest store state and showing new data
function render() {
  const state = store.getState();
  console.log('state: ', state);
}

// Update the UI with the initial data
render();
// And subscribe to redraw whenever the data changes in the future
store.subscribe(render);


ReactDOM.render(
  <React.StrictMode>
    <Provider store={store}>
      <App />
    </Provider>
  </React.StrictMode>,
  document.getElementById('root')
)

```

### reducers.js
```js
// Reducer
// 计数器加减
//---------
let initialState = {count: 0}
export default function countReducer(state = initialState, action) {
  switch (action.type) {
    case 'INCREMENT':
      return { count: state.count + 1 }

    case 'DECREMENT':
      return { count: state.count - 1 }
      
    default:
      return state
  }

}

```

### App.js

React-Redux是Redux的官方React绑定库。它能够使你的React组件从Redux store中读取数据，并且向store分发actions以更新数据。

它提供connect方法，用于从UI 组件生成容器组件。 connect的意思，就是将这两种组件连起来。 export default connect(mapStateToProps, mapDispatchToProps)(AppUI);

```js
import React, { Component } from 'react';
import { connect } from 'react-redux';


class App extends Component {

	constructor(props) {
		super(props);
	}

	componentDidMount() {

		//from `mapDispatchToProps()`
		this.props.handleIncrement();
	}


	render() {

		//from `mapStateToProps()`
		const preloadedState_count = this.props.count;

		function renderCounter() {
			return preloadedState_count !== null ? preloadedState_count : '';
		}


		return (
			<React.Fragment>
				<a href='#' onClick={() => { this.props.handleIncrement(); }}>Increment</a><br />
				<a href='#' onClick={() => { this.props.handleDecrement(); }}>Decrement</a><br />

				<h1>{renderCounter()}</h1>
			</React.Fragment>
		);
	}

}

const mapStateToProps = (state) => {

	return {
		count: state.count  //Receive redux
	}
};

const mapDispatchToProps = (dispatch) => {
	return {
		handleIncrement: () => dispatch({ type: 'INCREMENT' }),
		handleDecrement: () => dispatch({ type: 'DECREMENT' })
	}
}


export default connect(
	mapStateToProps,
	mapDispatchToProps
)(App);
```

### App-simple.js (无状态函数版本)
```js
import React, { Component } from 'react';
import { connect } from 'react-redux';


class App extends Component {

	/* 注意: increment = () => { ... } 这种写法无需使用 bind(this) */

	increment() {
		this.props.dispatch({
			type: "INCREMENT"
		});
	};

	decrement() {
		this.props.dispatch({
			type: "DECREMENT"
		});
	};


	componentDidMount() {
		this.increment();
	}



	render() {

		//from `mapStateToProps()`
		const preloadedState_count = this.props.count;

		function renderCounter() {
			return preloadedState_count !== null ? preloadedState_count : '';
		}


		return (
			<React.Fragment>
				<a href='#' onClick={this.increment.bind(this)}>Increment</a><br />
				<a href='#' onClick={this.decrement.bind(this)}>Decrement</a><br />

				<h1>{renderCounter()}</h1>
			</React.Fragment>
		);
	}

}

const mapStateToProps = state => {
	return {
		count: state.count
	};
};

export default connect(mapStateToProps)(App);

```

### App-hook.js (Hook版本)
```js
import React, { useEffect } from 'react';
import { useSelector, useDispatch } from 'react-redux';

const App = () => {

	//Receive redux (useSelector钩子在概念上大致相当于 mapStateToProps 参数来连接)
	const renderCounter = useSelector((state) => {
		return state.count
	}); 

	 // useDispatch钩子从 Redux 存储中返回对 dispatch 函数的引用
	const dispatch = useDispatch(); 


	const increment = () => {
		dispatch({
			type: "INCREMENT"
		});
	};

	const decrement = () => {
		dispatch({
			type: "DECREMENT"
		});
	};


	useEffect(() => {
		increment();
	}, [])


	return (
		<React.Fragment>
			<a href='#' onClick={increment}>Increment</a><br />
			<a href='#' onClick={decrement}>Decrement</a><br />

			<h1>{renderCounter}</h1>
		</React.Fragment>
	);
}

export default App;

```