---
title: Koa2+MongoDB+React撸个即时聊天web应用
date: 2020-02-10
tags: 
    - NodeJs
    - React
    - MongoDB
summary: 使用React+Koa2+MongoDB实现一个即时聊天的web应用
---

# Koa2+MongoDB+React撸个即时聊天web应用

学习了`Koa2`和`MongoDB`之后，突然就想着撸个实战项目出来看看，想来想去，还是搞个即时聊天应用出来玩玩吧。

项目的所有源码已经放到[Github](https://github.com/hugewilliam/chat_room/tree/simple_chat)
我也把最终的效果放到线上服务器了[摸鱼俱乐部聊天室](https://liwuhou.cn/chat)

前端部分我使用了`React`来搭建界面，工作用的都是`Vue`，感觉再不用用`React`就要生疏了……

## 前端部分
我使用的是`create-react-app`脚手架来搭建项目

### 搭建项目

用脚手架生成一个`chat`项目

```shell
# 如果没安装脚手架的就全局装下
$ yarn add global create-react-app

# 创建项目
$ yarn create react-app chat && cd chat

# 当然也可以用npm
$ npm install -g create-react-app
$ npm init react-app chat
```

完事后进入`chat`目录就可以看到以下的结构

```shell
.
├── README.md
├── package.json
├── node_modules/...
├── public
│   ├── favicon.ico
│   ├── index.html
│   ├── logo192.png
│   ├── logo512.png
│   ├── manifest.json
│   └── robots.txt
├── src
│   ├── App.css
│   ├── App.js
│   ├── App.test.js
│   ├── index.css
│   ├── index.js
│   ├── logo.svg
│   ├── serviceWorker.js
│   └── setupTests.js
├── yarn-error.log
└── yarn.lock
```

然后就是将脚手架生成的多余的文件给删掉，使目录结构变成这样

```shell
.
├── package.json
├── node_modules/...
├── public
│   ├── favicon.ico
│   ├── index.html
│   └── robots.txt
├── README.md
├── src
│   ├── App.jsx
│   └── index.js
└── yarn.lock

```
将src下的`app.js`改为`app.jsx`(这样看起来更酷不是吗？)，然后对文件内容进行一些更改。
```jsx {4, 6}
import React from 'react';

function App() {
	// 这里先输出hello world跟编程世界打个招呼
	return (
		<h1>hello world</h1>
	);
}

export default App;
```

将index.js文件也修改下

```js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

ReactDOM.render(
	<App/>,
	document.querySelector('#root')
)
```

**这一部分的源代码可以在git仓库里面的`init`分支上查看。**


### 配置路由及封装axios

这个demo中，我路由管理用的是`react-router-dom`，异步请求使用的是`axios`库。
话不多说，先安装相关依赖

```shell

$ yarn add react-router-dom axios 

```

#### 编写路由配置

由于这里只是一个很简单的小demo，所以路由并不多。其实也就两个，一个是初始进入时的注册和登录页(`/login`)，还有就是聊天界面(`/chat`)。
在`src`目录新建一个`router`目录，并在目录下放新建`index.js`文件，现在我们来编写应用里可能会用到的路由跳转。
大概撸出来，是这样的

```jsx

import React from 'react';
import {Route, Switch} from 'react-router-dom';
// 这里的页面组件还没有编辑，先占下位置
import Login from '../views/Login';
import Chat from '../views/Chat';

export default class Router extends React.Component{
	render(){
		return (
			<Switch>
				{/* 登录/注册 */}
				<Route exact path="/login" component={Login}/>
				{/* 聊天界面 */}
				<Route exact path="/chat" component={Chat}/>
			</Switch>
		)
	}
}

```

接着在`src`下新建`views`文件夹，往`Login`和`Chat`里随便写点东西。
最后往`App.jsx`中引入`Router`

```jsx {9, 10, 11}

// App.jsx
import React from 'react';
import {HashRouter} from 'react-router-dom';
import Router from './router';

export default function App(){
	return (
		<HashRouter>
			<Router/>
		</HashRouter>
	);
}

```

在终端中输入`yarn start`就可以启动服务，在浏览器中输入`localhost:3000/#/login`和`localhost:3000/#/chat`可以访问相应的组件了。
**这一部分的源代码可以在git仓库里面的`1-1`分支上查看。**

### eject和修改webpack配置

并且由于后续需要改一些`webpack`的配置，这里需要`yarn eject`一下，把脚手架隐藏了的`webpack`的配置都暴露出来。

```shell

$ yarn eject

```

执行之后发现多了个`config`和`scripts`目录，并且`package.json`文件也多了很多内容。详细的可以自行了解`create-react-app`里的`yarn eject`作用，我们先去改下`config`目录下的`webpack.config.js`配置。
> p.s. 对`webpack`不太了解的，可以先去看看我之前做的`webpack`配置的这篇文章——十分钟——[带你了解webpack的主要配置](https://liwuhou.cn/2020/01/07/%E5%8D%81%E5%88%86%E9%92%9F%E2%80%94%E2%80%94%E5%B8%A6%E4%BD%A0%E4%BA%86%E8%A7%A3webpack%E7%9A%84%E4%B8%BB%E8%A6%81%E9%85%8D%E7%BD%AE/)

这里加了两行`alias`(别名)配置，一个是让`webpack`将`@`跟`src`目录的绝对路径，另一个`utils`指向`src/utils`，从而能降低后续引用对应模块的路径的复杂度。

```js {6,7}
{
	// ... 其他配置
	alias: {
		// Support React Native Web
		'react-native': 'react-native-web',
		'@': path.resolve(__dirname, '../src'),
		'utils': path.resolve(__dirname, '../src/utils')
	},
}
```

现在，我们可以很方便的使用别名来引入了，去到`router`目录，修改下`index.js`试试。

``` jsx {2, 3}
// 将第五、第六行引入组件的代码用别名改写一下
import Login from '@/views/Login';
import Chat from '@/views/Chat';
```

重启服务，可以看到`Login`和`Chat`被正确引入了。
**这一部分的源代码可以在git仓库里面的`1-2`分支上查看。**


### 适配移动端

项目里我使用的是`rem`布局，在css中`em`是相对于父节点的`font-size`来参照大小的，而`rem`呢，就是参照`root`(根)节点，在浏览器中，这个根节点就是`html`标签。所以我们通过**获取用户的屏幕尺寸，通过一定比例来改变页面中`html`标签的`font-size`属性**，这样所有使用了`rem`单位的属性，就能响应式的完成不同屏幕尺寸的适配。

> 具体源码我放在`src/utils/fit2rem.js`文件中，原理我就不赘述了，感兴趣的朋友可以阅读源码+百度谷歌。
最后在`src/index.js`中引入

**接着引入全局样式**

虽然在React项目中，在组件js文件中引入的样式就是全局的，但是为了增加仪式感，同时也是让以后方便管理和定位问题，还是让我们在`index.js`中引入`css/index.scss`和`normalize.css`。

> normalize.css 是一个有别于传统reset.css的样式重置文件，相比之下，reset.css有时候显得太过暴力了，会造成一些不必要的性能损耗，而normalize.css就温柔多了...

这里记得安装下用到的`node-sass`依赖
```shell

# 这里如果有安装不了的同学，百度一下npm淘宝镜像，用cnpm安装一下
$ yarn add node-sass

```

> p.s. 这里引入的css文件就在仓库直接拷贝吧，我就不放出来了

在`index.js`中引入
```jsx {7, 8}

import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

// 由于我们这里将utils配置了别名，webpack会自己将utils解析为src下的utils目录
import 'utils/fit2rem';
import './index.scss';

ReactDOM.render(
	<App/>,
	document.getElementById('root')
);

```

**这一部分的源代码可以在git仓库里面的`1-3`分支上查看。**

### 实现简单的路由拦截

由于做的是一个即时聊天的demo，就要实现登录和注册功能了。这里先简单的使用`cookie`实现登录权限控制，后面有时间，才改写成`token`的形式吧（其实是我后端部分还不知道怎么弄）。

先封装两个获取`cookie`指的工具函数，编写于`utils/index.js`工具库中。

然后让我们改一下`router`，的给组件`Chat`(聊天界面)增加路由拦截，通过“简单粗暴”地判断是否在cookie中存有我们用户的用户名，来实现路由跳转。如果有一个`username`的cookie，那么就粗暴地认为用户已经登录获得了权限，如果没有，就跳转到`Login`(注册/登录)组件，让用户去注册或者登录。

```jsx {3, 14, 15, 16, 23}
// router/index.js文件中
// ...其他代码不变，引入Redirect重定向组件
import {Route, Switch, Redirect} from 'react-router-dom';
// 引入cookie工具函数
import {getCookie} from 'utils';

export default class Router extends React.component{
	render(){
		return (
			<Switch>
				{/* 登录/注册 */}
				<Route exact path="/login" component={Login}/>
				{/* 有登录权限的聊天界面 */}
				<PrivateRouter path="/">
					<Chat/>
				</PrivateRouter>
			</Switch>
		)
	}
}

// 登录权限控制
function PrivateRouter({children, props}) {
	return (
		<Route
			{...props}
			render={({location}) => {
				const hasAuthority = getCookie('username');
				return (
					hasAuthority ? 
					children : 
					<Redirect
						to={{
							pathname: 'login',
							state: {from: location}
						}}
					/>
				)
			}}
		/>
	)
})
```

此时已经可以在项目中尝试输入非`/login`的path的时候，都会跳转到login里面了，这是因为我们没有请求接口，也没有手动往浏览器的`cookie`中写入`username`，所以一直被浏览器跳转到`/login`路由的缘故，当我们直接在控制台写入一条`username`的cookie时，就会发现已经可以为所欲为的去任何路由了。

```js
// 在浏览器控制台中，直接操作cookie
document.cookie='username=willliam';
// 在修改浏览器地址栏的地址，就发现不会被拦截在login路由之外了
```

**这一部分的源代码可以在git仓库里面的`1-4`分支上查看。**

