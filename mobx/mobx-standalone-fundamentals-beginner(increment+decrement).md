# mobx-standalone-fundamentals-beginner(increment+decrement)

## File Structures

```sh
src/
└── index.js (Entry file, for client/browser)
```


## Installation And Test


**Step 1.** First, using an absolute path into your app folder directory.

```sh
$ cd /{your_directory}/mobx-standalone-fundamentals-beginner\(increment+decrement\)
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

function render(res) {
  document.getElementById('root').innerHTML = JSON.stringify(res);
}

render(store);

//actions
document.getElementById('button-1').addEventListener( 'click', function(e) {
  store.increase();
  render(store);
});

document.getElementById('button-2').addEventListener( 'click', function(e) {
  store.decrease();
  render(store);
});

```

The decorator syntax can be written as:
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

Use decorate to import:
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
	<title>Mobx Standalone</title>
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

