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

> 项目的所有源码已经放到[Github](https://github.com/hugewilliam/chat_room/tree/simple_chat)
> 我也把最终的效果放到线上服务器了[摸鱼俱乐部聊天室](https://liwuhou.cn/chat)

前端部分我使用了`React`来搭建界面，工作用的都是`Vue`，感觉再不用用`React`就要生疏了……

## 前端部分
我使用的是`create-react-app`脚手架来搭建项目

### 搭建项目

```shell
# 如果没安装脚手架的就全局装下
yarn add global create-react-app

# 创建项目
yarn create react-app chat && cd chat

# 当然也可以用npm
npm install -g create-react-app
npm init react-app chat
```

然后先将src目录改为如下结构：

```shell
src/
├── index.js
├── index.scss # 全局css样式
├── App.jsx 
├── components # 组件目录
├── setupProxy.js # 可以配置一些代理服务
├── api # 存放api接口配置
│   ├── index.js
│   └── serverApi.js
├── axios # 简单封装下axios
│   └── index.js
├── css # 存放全局的css样式或者方法
│   ├── common.scss
│   ├── index.scss
│   └── normalize.css
├── router # 路由配置
│   └── index.js
├── utils # 可能用到的一些工具函数
│   ├── fit2rem.js
│   └── index.js
└── views # 页面文件
    ├── Chat
    │   ├── index.jsx
    │   └── index.scss
    └── Login
        ├── index.jsx
        └── index.scss
```

安装项目中用到的依赖

```shell
yarn add react-router-dom axios 
# 这里我用的是sass所以还需要再安装下`ass的依赖到项目里
yarn add node-sass sass
# 我用了socket.io 实现跟服务器的websocket接口的对接，所以装下socket.io-client
yarn add socket.io-client
```

### 修改webpack配置

并且由于后续需要改一些`webpack`的配置，这里需要`yarn eject`一下，把`webpack`的配置都暴露出来

```shell
yarn eject
```

执行之后发现多了个`config`和`scripts`目录，并且`package`也多了很多内容。详细的可以自行了解`create-react-app`里的`yarn eject`作用，我们先去改下`config`目录下的`webpack.config.js`配置。
p.s. 对`webpack`不太了解的，可以先去看看我之前做的`webpack`配置的这篇文章——十分钟——[带你了解webpack的主要配置](https://liwuhou.cn/2020/01/07/%E5%8D%81%E5%88%86%E9%92%9F%E2%80%94%E2%80%94%E5%B8%A6%E4%BD%A0%E4%BA%86%E8%A7%A3webpack%E7%9A%84%E4%B8%BB%E8%A6%81%E9%85%8D%E7%BD%AE/)

```js {7,8}
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

### 适配移动端

项目里我使用的是`rem`布局，在css中`em`是相对于父节点的`font-size`来参照大小的，而`rem`呢，就是参照root节点，在浏览器中，这个根节点就是`html`标签。所以我们通过获取用户的屏幕尺寸，通过一定比例来改变页面中`html`标签的`font-size`属性，这样所有使用了`rem`单位的属性，就能实现不同屏幕尺寸的适配。

具体源码我放在`src/utils/fit2rem.js`文件中，我就不赘述了。
然后在`src/index.js`中引入






关注本人公众号

![](https://blogs-1257826393.cos.ap-shenzhen-fsi.myqcloud.com/qrcode_for_gh_373ae200ef34_344.jpg)