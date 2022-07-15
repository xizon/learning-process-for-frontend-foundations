# redux-standalone-fundamentals-beginner(increment+decrement)


## File Structures

```sh
src/
├── reducers.js
└── index.js (Entry file, for client/browser)
```


## Installation And Test


**Step 1.** First, using an absolute path into your app folder directory.

```sh
$ cd /{your_directory}/redux-standalone-fundamentals-beginner\(increment+decrement\)
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


//
function render(res) {
  document.getElementById('root').innerHTML = JSON.stringify(res);
}

render(store.getState());

//actions
document.getElementById('button-1').addEventListener( 'click', function(e) {
  store.dispatch({ type: 'INCREMENT' });
  render(store.getState());
});

document.getElementById('button-2').addEventListener( 'click', function(e) {
  store.dispatch({ type: 'DECREMENT' });
  render(store.getState());
});
```

### reducers.js

```js
// Reducer
// Counter reaction
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


### index.html

```html
<!DOCTYPE html>
<html lang="en">

<head>
	<meta charset="utf-8">
	<meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
	<meta name="theme-color" content="#000000">
	<!--
      manifest.json provides metadata used when your web app is added to the
      homescreen on Android. See https://developers.google.com/web/fundamentals/engage-and-retain/web-app-manifest/
    -->
	<link rel="manifest" href="/manifest.json">
	<title>Redux Standalone</title>
</head>

<body>
	<noscript>
		You need to enable JavaScript to run this app.
	</noscript>
	<button id="button-1">increment</button>
	<button id="button-2">decrement</button>
	
	<div id="root"></div>
	<!--
      This HTML file is a template.
      If you open it directly in the browser, you will see an empty page.

      You can add webfonts, meta tags, or analytics to this file.
      The build step will place the bundled scripts into the <body> tag.

      To begin the development, run `npm start` or `yarn start`.
      To create a production bundle, use `npm run build` or `yarn build`.
    -->
</body>

</html>
```