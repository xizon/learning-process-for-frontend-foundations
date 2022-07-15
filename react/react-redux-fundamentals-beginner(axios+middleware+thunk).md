# react-redux-fundamentals-beginner(axios+middleware+thunk)

## File Structures

```sh
src/
├── reducers.js
├── createStore.js
├── getJSONData.js
├── index.js (入口文件，用于客户端/浏览器)
└── App.js (React组件，需要自己放到路由配置中)
```

SSR参考：
```sh
src/server/ 
└── server.js (用于Express服务端渲染)
```

## Installation And Test


**Step 1.** First, using an absolute path into your app folder directory.

```sh
$ cd /{your_directory}/react-redux-fundamentals-beginner\(axios+middleware+thunk\)
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


//
import createStore from './createStore';
import App from './App';


const store = createStore();

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

// Reducer 1
// 异步获取http请求
//---------
export default function theDefaultReducer(state = [], action) {
  switch (action.type) {
    case 'RECEIVE_DEMO_LIST':
      return action.payload;

    default:
      return state
  }
}

// Reducer 2
// 计数器加减
//---------
export function countReducer(state = {count: 0}, action) {
  switch (action.type) {
    case 'INCREMENT':
      return { count: state.count + 1 }

    case 'DECREMENT':
      return { count: state.count - 1 }
      
    default:
      return state
  }

}


// Reducer 3
// 默认的固定数值
//---------
export const firstNamedReducer = (state = 1, action) => state

// Reducer 4
// 默认的固定数值
//---------
export const secondNamedReducer = (state = 2, action) => state

```

### createStore.js

Redux Thunk中间件使您可以编写返回函数而不是操作的操作创建者。重击程序可用于延迟操作的分发，或者仅在满足特定条件时调度。内部函数接收存储方法dispatch和getState作为参数。
如您所见，它将返回一个函数而不是一个动作，这意味着您可以随时等待并调用它，因为它是一个函数。

Middleware 中间件它在 dispatch action 和到达 reducer 的那一刻之间提供了逻辑插入点。可以使用 Redux 中间件进行日志记录、异常监控、与异步 API 对话、路由等。

```js
import { combineReducers, createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';

import theDefaultReducer, {
  countReducer,
  firstNamedReducer,
  secondNamedReducer
} from './reducers.js'

// Use ES6 object literal shorthand syntax to define the object shape
const rootReducer = combineReducers({
  reNameDefaultReducer: theDefaultReducer,
  countReducer,
  firstNamedReducer,
  secondNamedReducer
});


export default () => {
	
    /*
    const store = createStore(rootReducer)
    console.log(store.getState())

    Output: 
    {
        countReducer: {count: 0},
        reNameDefaultReducer : [{name: 'XXX'},{name: 'XXX'}], 
        firstNamedReducer : 1, 
        secondNamedReducer : 2
    }
    */

    const _initialState = {}; //JSON
    const _reducer = rootReducer; //Function
    const _enhancer = applyMiddleware(thunk); //Function
    const store = createStore(_reducer, _initialState, _enhancer);
	
    return store;
};


```

### getJSONData.js
```js

import axios from 'axios';

const getJSONData = async (name) => {

    // Wait for all the `httpRequest` functions, if they are resolved, run 'store.dispatch()'
    const httpRequest = () => {
      return new Promise( (resolve,reject) => {
        axios({
          timeout: 15000,
          method: 'get',
          url: `https://restcountries.com/v2/name/${name}`,
          responseType: 'json'
        }).then(function (response) {
          resolve( response );
        })
        .catch(function (error) {
          console.log( error );
        });
      });
    };
  
    const getApiData = await httpRequest();
    return getApiData.data;
  
}

export default getJSONData;

```

### App.js
React-Redux是Redux的官方React绑定库。它能够使你的React组件从Redux store中读取数据，并且向store分发actions以更新数据。

它提供connect方法，用于从UI 组件生成容器组件。 connect的意思，就是将这两种组件连起来。 export default connect(mapStateToProps, mapDispatchToProps)(AppUI);

```js
import React, { Component } from 'react';
import { connect } from 'react-redux';

import getJSONData from './getJSONData';

class App extends Component {

	constructor(props) {
		super(props);
	}


	componentDidMount() {

      //from `mapDispatchToProps()`
      this.props.handleGetDataFr();   
	}


	render() {

		//from `mapStateToProps()`
		const preloadedState_httpData = this.props.httpData;
    const preloadedState_count = this.props.count;


    function renderList() {
      if ( preloadedState_httpData !== null ) {
        const list = preloadedState_httpData.map((item, index) => {
          return <li key={index}>{item.name}</li>;
        });
      
        return <ul>{list}</ul>;
      } else {
        return '';
      }

    }

    function renderCounter() {
      
      return preloadedState_count !== null ? preloadedState_count : '';
    }


		return (
      <React.Fragment>
        <a href='#' onClick={() => { this.props.handleGetDataFr(); }}>Asynchronous Fetch Data(fr)</a><br />
        <a href='#' onClick={() => { this.props.handleGetDataUs(); }}> Asynchronous Fetch Data(us)</a><br />
        <a href='#' onClick={() => { this.props.handleIncrement();}}>Counter ({renderCounter()})</a>

        {renderList()}
      </React.Fragment>
		);
	}

}

const mapStateToProps = (state) => {
	const { reNameDefaultReducer, countReducer } = state; //Receive redux

	return {
		httpData: reNameDefaultReducer,
    count: countReducer.count
	}
};

const mapDispatchToProps = (dispatch) => {
	return {
		handleGetDataFr: async () => dispatch({ type: 'RECEIVE_DEMO_LIST', payload: await getJSONData('fr') }),
    handleGetDataUs: async () => dispatch({ type: 'RECEIVE_DEMO_LIST', payload: await getJSONData('us') }),
    handleIncrement: () => dispatch({ type: 'INCREMENT'})
	}
}


export default connect(
	mapStateToProps,
	mapDispatchToProps
)(App);
```

### server/server.js 【参考】

提示：你也可以将这里的axios方法封装成一个action creator单独export，这个action creator可以放到App.js中使用（组件可以被路由访问到，所以也可以访问到你在组件中创建的异步请求方法

```js
import express from 'express';
import axios from 'axios';

//
import ReactDOMServer from 'react-dom/server';
import { Provider } from 'react-redux';
import { StaticRouter } from 'react-router-dom';

import createStore from '../createStore';
import App from '../App';


const port = process.env.port || 3000;
const app = express();

app.get('*', (req, res) => {
    
    const store = createStore();

    store.dispatch(async function(dispatch) {

        // Wait for all the `httpRequest` functions, if they are resolved, run 'store.dispatch()'
        const httpRequest = () => {
            return new Promise( (resolve,reject) => {
                axios({
                    timeout: 15000,
                    method: 'get',
                    url: `https://examples.com`,
                    responseType: 'json'
                }).then(function (response) {
                    resolve( response );
                })
                .catch(function (error) {
                    console.log( error );
                });
            });
        };


        const getApiData = await httpRequest();
        const action = {
            type: 'RECEIVE_DEMO_LIST',
            payload: getApiData.data
        }
        dispatch( action );



        // Send the rendered html to browser.
        if (err) {
            console.error('Something went wrong:', err);
            return res.status(500).send('Oops, better luck next time!');
        } 

        //
        const context = {};



        // Render template
        const HTMLTmpl = `
            <!DOCTYPE html>
            <html lang="en">
            <head>
                <meta charset="UTF-8">
                <meta http-equiv="X-UA-Compatible" content="IE=edge">
                <meta name="viewport" content="width=device-width, initial-scale=1.0">
                <title>Document</title>
            </head>
            <body>
                <div id="app">{{reactApp}}</div>
		
	        <script>window.__PRELOADED_STATE__ = {{preloadedState}};</script>
	        <script src="dist.js"></script>
            </body>
            </html>
        `;


        let template;
        if (template != null && template != '' && typeof template != typeof undefined) {

            const reactContent = ReactDOMServer.renderToString(
                <Provider store={store}>
                <StaticRouter location={pathname} context={context}>
                   <App /> 
                </StaticRouter>
              </Provider>
            );
          

            template = HTMLTmpl.replace('{{reactApp}}', reactContent)
            .replace('{{preloadedState}}', JSON.stringify(store.getState()));
      
            //do something...

        }
      

        if (context.notFound) {
            res.status(404);
        }

        res.send(template);


    });


});

app.listen(port, () => console.log(`Frontend service ok`));
```



