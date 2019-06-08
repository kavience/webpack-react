## webpack手动搭建React项目
#### 初始化项目
新建一个目录，然后初始化项目
```
npm init
```

在package.json中加入以下依赖
```
// dependencies是指在使用npm i -save [modules] 的时候安装的，执行 npm install --production 的时候会安装
"dependencies": {
    "react": "^16.8.6",
    "react-dom": "^16.8.6",
    "react-router-dom": "^5.0.1"
  },
// devDependencies是指在使用npm i -save-dev [modules] 的时候安装的，执行 npm install --production 的时候不会安装
"devDependencies": {
// 安装babel相关的
"@babel/core": "^7.4.5",
"@babel/preset-env": "^7.4.5",
"@babel/preset-react": "^7.0.0",
"babel-loader": "^8.0.6",
"babel-polyfill": "^6.26.0",
// 插件：每次build的时候清空旧文件
"clean-webpack-plugin": "^3.0.0",
// css处理
"css-loader": "^2.1.1",
// 处理CSS兼容
"postcss-loader": "^3.0.0",
"autoprefixer": "^9.6.0",
// 添加less支持
"less": "^3.9.0",
"less-loader": "^5.0.0",
// 插件
"html-webpack-plugin": "^3.2.0",
"style-loader": "^0.23.1",
// 添加webpack打包
"webpack": "^4.33.0",
"webpack-cli": "^3.3.3",
// 提供web容器，可在本地访问http://localhost:port
"webpack-dev-server": "^3.7.1",
// 提供配置文件的合并
"webpack-merge": "^4.2.1"
}
```
### 安装以上依赖
```
npm install
```
### 配置bable
在根目录下新建一个.babelrc文件
```
{
    "presets": [
        "@babel/preset-env",
        "@babel/preset-react",
    ]
}
```
### 在根目录增加一个postcss.config.js
```
module.exports = {  
    plugins: {  
      'autoprefixer': {browsers: 'last 5 version'}  
    }  
  } 
```
### 在根目录新建一个webpack.base.config.js作为基础webpack的配置
```
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const { CleanWebpackPlugin } = require('clean-webpack-plugin');

module.exports = {
	// 入口文件
    entry: './src/index.js',
    output: {
        filename: 'bundle.[hash].js',
        path: path.join(__dirname, '/dist')
    },
    module: {
    	// 配置相应的规则
        rules: [
            {
                test: /\.css$/,
                use: ['style-loader', 'css-loader', 'postcss-loader']
            }, {
                test: /\.js[x]?$/,
                use: 'babel-loader',
                exclude: /node_modules/
            }, {
                test: /\.less$/,
                use: ['style-loader',
                    { loader: 'css-loader', options: { importLoaders: 1 } },
                    'less-loader'],
            }
        ]
    },
    // 配置相应的插件
    plugins: [
        new HtmlWebpackPlugin({
            template: './src/index.html'
        }),
        new CleanWebpackPlugin()
    ]
};

```

### 在根目录下新建webpack.dev.config.js文件作为开发环境的配置
```
const merge = require('webpack-merge');
const baseConfig = require('./webpack.base.config.js');

module.exports = merge(baseConfig, {
	// 设置为开发模式
    mode: 'development',
    devtool: 'inline-source-map',
    // 配置服务端目录和端口
    devServer: {
        contentBase: './dist',
        port: 3000
    }
});

```
### 在根目录下新建webpack.product.config.js文件作为生产环境的配置
```
const merge = require('webpack-merge');
const baseConfig = require('./webpack.base.config.js');

module.exports = merge(baseConfig, {
	// 设置为生产模式
    mode: 'production'
});

```

### 配置script命令
```
  "scripts": {
    "start": "webpack-dev-server --open --config webpack.dev.config.js",
    "build": "webpack --config webpack.product.config.js"
  },
```

### 在根目录新建src目录，和index.html
```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>

<body>
    <div id="root"></div>
</body>
</html>

```
### 在src新建入口文件index.js和App.js
```
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import './index.less';

ReactDOM.render(<App/>, document.getElementById('root'));

```

```
import React from 'react';

class App extends React.Component {
    render() {
        return <div>
            <h1 className="hello">Hello React & Webpack!</h1>
        </div>
    }
}

export default App;
```

### 在src目录新建index.less文件，测试less文件
```
@blog-color-red: #ff0000;

.hello {
    color: @blog-color-red;
}

```

### 运行程序
```
npm run start
```
不出意外的话，执行之后会跳到浏览器界面，并且显示红色的**Hello React & Webpack!**字样

### 打包文件
```
npm run build
```

不出意外的话会在根目录下生成dist文件，可以在文件夹中之间打开该文件，显示红色的**Hello React & Webpack!**字样

### 结语
之前也是一般选用框架自带的脚手架工具，趁着端午有时间，主动配置一下webpack & react，当然以上只是一些简单的配置，更复杂的配置还需要修改webpack配置，有时间再补充。接下来[配置webpack & vue](https://github.com/kavience/webpack-vue)。





