如何利用Facebook的create-react-app脚手架创建一个基于ant design mobile的项目
====
#### 引言：<br>
<br>
  `create-react-app`是`Facebook`发布的一款全局的命令行工具用来创建一个新的项目。<br>
<br>

  通常我们开始做一个`react web`或者 `app` 项目的时候，都会自己利用 `npm` 或者 `yarn` 安装项目所需要的一些依赖，再写 `webpack.config.js` ,一系列复杂的配置,搭建好开发环境后写src源代码。<br>
<br>
  现在，如果你想要搭建一个完整的 `react` 项目环境，已经不需要自己动手布置许许多多的东西，利用 `create-react-app` 工具，就能轻松帮你配置好一个 `react` 项目。<br>
<br>
#### 全局安装 `create-react-app`<br>

```
npm i create-react-app -g
```
#### 创建一个应用程序<br>

```
create-react-app ProjectName
```

#### 然后你可以看到创建完之后的目录结构<br>

```
my-app/
--README.md
--node_modules/
--package.json
--.gitignore
--public/
----favicon.ico
----index.html
--src/
----App.css
----App.js
----App.test.js
----index.css
----index.js
----logo.svg
```
#### 你会发现在整个项目文件夹中，并没有 `webpack.config.js` 文件，这时候你只需要在项目目录中执行 `npm run eject` 命令，你会看到他提示你 `Are you sure you want to eject? This is permanent.` 输入y,回车，打开项目文件夹你会发现多了两个目录（ `public` 、 `scripts`），像这样：<br>
![Image text](http://images2017.cnblogs.com/blog/1165657/201708/1165657-20170821112527339-503510306.png)<br>
<br>
####  试着运行一下项目，输入 `npm run start` 回车，浏览器会自动打开一个地址为 `http://localhost:3000` 的页面，端口号为3000.<br>
![Image text](http://images2017.cnblogs.com/blog/1165657/201708/1165657-20170821112937621-1367375730.png)<br>
<br>
#### 到这里，一个react项目基本上已经搭建完成了，现在我们为项目引入 `ant design mobile` 框架。<br>

#### 使用yarn安装：<br>

```
    yarn add antd-mobile
```
#### 使用npm安装：<br>
```
    npm install antd-mobile -D
```

#### 安装其他的一些开发依赖<br>

```
yarn add --dev babel-plugin-import svg-sprite-loader@0.3.1 less less-loader postcss-pxtorem
```

#### 接下来是项目的配置： <br>
<br>
  打开 config 文件夹中的 `webpack.config.dev.js` 文件，在 `webpack.config.dev.js` 中找到 `exclude` 追加两行代码，用于加载 `less` 和 `svg` 文件<br>
<br>
```
module.exports = {
    module: {
        rules: [
            ...
            {
                exclude: [
                  /\.html$/,
                  /\.js$/,
                  /\.json$/,
                  /\.less$/,//追加
                  /\.svg$/,//追加
                ],
                ...
            }
        ]
    }
}
```
#### 添加按需加载文件处理插件<br>
```
module.exports = {
    module: {
    rules: [
        {
            test: /\.(js|jsx)$/,
            ...
            options: {
              //追加
                plugins: [
                    ['import', { libraryName: 'antd-mobile', style: true }],
                ],
            ...
            },
        },
    ]
  }
}
```
#### 添加svg处理<br>
```
module.exports = {
  module: {
    rules: [
      ...
      {
        test: /\.(svg)$/i,
        loader: 'svg-sprite-loader',
        include: [
          require.resolve('antd-mobile').replace(/warn\.js$/, ''),  // antd-mobile使用的svg目录
          path.resolve(__dirname, '../src/'),  // 个人的svg文件目录，如果自己有svg需要在这里配置
        ]
        },
      ...
    ]
  }
}
```
#### 添加less处理<br>
```
module.exports = {
  module: {
    rules: [
      ...
       {
        test: /\.less$/,
        use: [
          require.resolve('style-loader'),
          require.resolve('css-loader'),
          {
          loader: require.resolve('postcss-loader'),
          options: {
            ident: 'postcss', // https://webpack.js.org/guides/migrating/#complex-options
            plugins: () => [
            autoprefixer({
              browsers: ['last 2 versions', 'Firefox ESR', '> 1%', 'ie >= 8', 'iOS >= 8', 'Android >= 4'],
            }),
            pxtorem({ rootValue: 100, propWhiteList: [] })
            ],
          },
          },
          {
          loader: require.resolve('less-loader'),
          options: {
            modifyVars: { "@primary-color": "#1DA57A" },
          },
          },
        ],
        }
      ...
    ]
  }
}
```

#### 在此之前必须要引入 `postcss-pxtorem` 模块，用于px转rem<br>

```
const pxtorem = require('postcss-pxtorem');
```
#### 重新启动项目，但是你会发现react的Logo没了。此时就轮到 `antd--design-mobile` 闪亮登场了。 <br>
<br>
#### 在 src/App.js 中导入antd-mobile的Icon,代码如下：<br>
<br>

```
import React, { Component } from 'react';
import {Icon} from 'antd-mobile';
import logo from './logo.svg';
import './App.css';

class App extends Component {
  render() {
  return (
    <div className="App">
    <div className="App-header">
      <Icon type={logo} />
      <h2>Welcome to React</h2>
    </div>
    <p className="App-intro">
      To get started, edit <code>src/App.js</code> and save to reload.
    </p>
    </div>
  );
  }
}
export default App;
```
<br>

最后是使用antd-mobile提供的高清解决方案，你可以参考
[官方文档](https://github.com/ant-design/ant-design-mobile/wiki/antd-mobile-0.8-%E4%BB%A5%E4%B8%8A%E7%89%88%E6%9C%AC%E3%80%8C%E9%AB%98%E6%B8%85%E3%80%8D%E6%96%B9%E6%A1%88%E8%AE%BE%E7%BD%AE) 或者按照以下步骤：
-

下载 
[viewport.js](https://gw.alipayobjects.com/os/rmsportal/dVgyohpfmDMFFeDasFns.js)
或者下载压缩版的 
[viewport.min.js](https://gw.alipayobjects.com/os/rmsportal/uDTmsEBmTUVrpmCBozbm.js) 

#### 在 public 目录下的 index.html 中引入下载好的js，请内联写到所有 css 引用之前, 否则部分安卓机有问题，并且不要再设置meta标签的viewport

打开config/webpack.config.dev.js，新增一句代码<br>

```
{
    test: /\.css$/,
    use: [
      require.resolve('style-loader'), {
        loader: require.resolve('css-loader'),
        options: {
          importLoaders: 1,
        }
      }, {
        loader: require.resolve('postcss-loader'),
        options: {
          ident: 'postcss',
          plugins: () => [
            require('postcss-flexbugs-fixes'),
            autoprefixer({
              browsers: [
                '>1%',
                'last 4 versions',
                'Firefox ESR',
                'not ie < 9', // React doesn't support IE8 anyway
              ],
              flexbox: 'no-2009',
            }),
            pxtorem({ rootValue: 100, propWhiteList: [] }) //新增
          ],
        },
      },
    ],
  },
  ```
#### 重启项目，你会发现css单位由px转为了rem。
#### 至此，一个基于ant design mobile 的项目就搭建完成啦！
