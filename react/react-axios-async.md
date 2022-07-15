# React“视图”和“逻辑”分离+异步请求

### 参考代码
```js

import React from 'react';
import axios from 'axios';

//“视图”和“逻辑”分离
class DemoPresenter {
	data = {};
	constructor( props ) {
		this.props = props;
	}

	fetchData = async () => {
		const res = await axios.get(`https://restcountries.com/v2/name/china`);
		console.log( res );
		this.data = res.data;
	}

	handleClick = () => {
		//do something...
		console.log( 'click data:', this.data );
	}

	bar() {
		console.log( 'bar' );
	}

}


class Demo extends React.Component {
	constructor(props) {
		super(props);
		this.presenter = new DemoPresenter();

	}
	
// 	调用返回类型为 Promise<void> 而不使用 await 关键字的方法与调用返回类型为 void 的方法没有区别。
// 	由于在 componentDidMount() 延迟执行之后没有生命周期方法似乎很安全，目前也不会出什么问题
// 	也可以不延迟 componentDidMount()
// 	componentDidMount() {
// 	    axios.get('URL')
// 	    .then(function (response) {
// 		// handle success
// 		console.log(response);
// 	    })
// 	    .catch(function (error) {
// 		// handle error
// 		console.log(error);
// 	    })
// 	    .then(function () {
// 		// always executed
// 	    });
// 	}

	//这里也要使用async...await
	async componentDidMount() {
		//加载中
		this.loadingRef.innerHTML = 'loading...';

		const {fetchData} = this.presenter;
		await fetchData();

		//加载完成
		const {data} = this.presenter;
		this.loadingRef.innerHTML = JSON.stringify(data);


	}

	render() {
		const {data,handleClick} = this.presenter;
		console.log( data ); // 始终为 {}

		return (
			<>
				<a href="#" onClick={handleClick}>
					点击这里(也可以看到请求成功的数据)
				</a>
				<p ref={loadingRef => (this.loadingRef = loadingRef)}></p>
			</>
		);
	}
}



ReactDOM.render(
	<Demo />,
	document.getElementById('app')
);
```