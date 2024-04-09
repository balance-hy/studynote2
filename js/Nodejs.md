#  Node.js

## npm构建工程

> https://blog.csdn.net/weixin_41619240/article/details/109787518

```shell
npm init  #自己选择

npm init -y #默认生成
```

![image-20231027123117926](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202310271231243.png)

```json
{
  "name": "balance01", // 工程名
  "version": "1.0.0", // 版本
  "description": "我是一个node工程", // 描述
  "main": "index.js", // 入口js
  "scripts": { // 运行脚本
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [ // 关键字,用关键字描述项目
    "node"
  ],
  "author": "balance", //开发者
  "license": "ISC" // 授权协议
}
```

## npm 安装/卸载模块

### 快速安装第三方模块

在项目目录下命令行窗口输入：

```shell
npm install xxx

#指定淘宝仓库，加速下载
npm install --registry=https://registry.npm.taobao.org
#或者
npm i xxx

#若要指定版本 后面加@版本号
npm i redis@3.0.2
```

**安装的模块会自动放入到项目的node_modules文件夹中**

若无node_modules文件夹，会自动创建，并且会创建package-lock.json文件

在package.json文件中会增加一个属性即依赖项

```json
"dependencies": {
    "redis": "^4.6.10"
}
```

在之后，如果需要重新构建新项目，也用到这些第三方模块，只需要将package.json复制过去，然后输入命令

```shell
npm install
```

会直接下载package.json中所有的依赖项

### 如何使用模块 redis为例

先导入

```js
import { createClient } from 'redis';
```

再使用

```js
import { createClient } from 'redis';

const client = await createClient()
  .on('error', err => console.log('Redis Client Error', err))
  .connect();

await client.set('key', 'value');
const value = await client.get('key');
await client.disconnect();
```

**或者使用require导入**

```js
const myModule = require('./myModule');
```

#### require 和 import

> https://www.jb51.net/article/276886.htm

`import`只能导入ES6模块或者使用Babel等工具转化为ES6模块的代码。而`require`则可以导入CommonJS模块、AMD模块、UMD模块以及Node.js内置模块等多种类型的模块。

### 安装缓慢

先换成阿里云的源，之后使用指令为cnpm install xxx，即可

![image-20231027130449969](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202310271304845.png)

### 卸载模块

```shell
npm uninstall xxx
```

### package/package-lock

> https://www.php.cn/faq/495596.html

我们每次，去npm install xxx会把内容记录到package.json文件中，下载的包都会发生变化。

为了系统的稳定性考虑，每次执行完npm install之后会对应生成package-lock文件，该文件记录了上一次安装的具体的版本号。

根据官方文档，package-lock.json 是生成的系统当前安装的库的具体来源和版本号，锁定版本。

当你执行npm install的时候， node会先从package.json文件中读取所有dependencies信息，然后根据dependencies中的信息与node_modules中的模块进行对比，没有的直接下载，==node是从package.json文件读取模块名称，从package-lock.json文件中获取版本号，然后进行下载或者更新==。

**当package.json与package-lock.json都不存在**。

执行"npm install"时，node会重新生成package-lock.json文件，然后把node_modules中的模块信息全部记入package-lock.json文件，但不会生成package.json文件。但是，你可以通过"npm init --yes"来生成package.json文件

## Babel Es6->Es5

> [babel安装及使用详解](https://blog.csdn.net/caiqian97/article/details/129726671)
>
> [babel官方中文文档](https://www.babeljs.cn/docs/configuration)

Es6的语法无法在一些浏览器甚至node.js中使用，Babel(转码器)用于将Es6的代码转换成Es5。

### 安装

Babel 提供babel-cli工具，用于命令行转码。安装命令如下

```shell
npm install --save-dev @babel/core @babel/cli @babel/preset-env
#注意早期版本包括视频都是 npm install -g babel-cli
#这表示在全局安装-g(-global) 现在用上面的命令，现在也不要使用-g，这样会导致项目对环境的依赖，全集成到项目中即可。

# 查看是否安装成功
babel --version
```

### 配置

> [配置](https://www.babeljs.cn/docs/configuration)

新建.babelrc.json文件在根目录下，复制下列配置进去

```json
{
  "presets": ["@babel/env"],
  "plugins": []
}
```

或者，还可以选择将 [`.babelrc.json`](https://www.babeljs.cn/docs/configuration#babelrcjson) 中的配置信息作为 `babel` 键（key）的值添加到 `package.json` 文件中，如下所示：

```json
{
  "name": "my-package",
  "version": "1.0.0",
  "babel": {
    "presets": [ ... ],
    "plugins": [ ... ],
  }
}
```

更多详见配置和babel安装及使用详解

这里解释一下presets属性，其实就是预设，也就是转码器，之前的版本是直接指定比如es2015，现在只需要@babel/env，这集成了之前的很多插件，可以向里传递参数。

### 使用

安装完成后，执行下面的命令进行转码，该命令含义是把main.js转码生成compiled.js文件

```shell
npx babel main.js -o compiled.js
```

也可以指定目录下的所有文件转码到指定目录,下面的命令是把src目录下文件转码到dist目录下

```shell
npx babel src --out-dir dist
```

#### 自定义脚本使用

每次都需要输入使用的命令无疑很长也很容易输错

可以增加package.json中script对象中属性，为其取一个名字，比如build

```json
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "babel src\\example.js -o dist\\compiled.js"
},
```

或者,上面是指定文件写法，下面是目录写法

```json
"build": "babel src -o dist"
```

转码时候，执行下面的命令

```shell
mkdir dist # 若无dist目录，先创建
npm run build
```

## webpack

> [webpack安装与使用](https://blog.csdn.net/qq_43812504/article/details/125455896)
>
> [官方文档](https://webpack.docschina.org/guides/getting-started/#basic-setup)

### 安装

首先我们创建一个目录，初始化 npm，然后 [在本地安装 webpack](https://webpack.docschina.org/guides/installation#local-installation)，接着安装 [`webpack-cli`](https://github.com/webpack/webpack-cli)（此工具用于在命令行中运行 webpack）：

```shell
npm install webpack webpack-cli --save-dev
```

安装一个将被打包到生产环境 bundle 的 package 时，应该使用 `npm install --save`；而在安装一个用于开发环境的 package 时（例如，linter、测试库等），应该使用 `npm install --save-dev`。更多信息请查看 [npm 文档](https://docs.npmjs.com/cli/install)。

```
当你为你的模块安装一个依赖模块时，正常情况下你得先安装他们（在模块根目录下npm install module-name），然后连同版本号手动将他们添加到模块配置文件package.json中的依赖里（dependencies）。

-save和save-dev可以省掉你手动修改package.json文件的步骤。
```

### 配置

在 webpack v4 中，无须任何配置即可运行，然而大多数项目会需要很复杂的设置，这就是为什么 webpack 仍然要支持 [配置文件](https://webpack.docschina.org/concepts/configuration)。这比在 terminal（终端）中手动输入大量命令要更加高效，所以让我们创建一个配置文件：

在根目录下创建**webpack.config.js**

```js
const path = require('path');

module.exports = {
  entry: './src/main.js',//指定入口
  output: {
    filename: 'index.js',//制定出口
    path: path.resolve(__dirname, 'dist'),//__dirname具体查看node文档，意思是在根目录dist目录
  },
};
```

构建

```shell
webpack --config webpack.config.js
# 默认直接webpack即可
```

如果 `webpack.config .js` 存在，则 `webpack` 命令将默认选择使用它。我们在这里使用 `--config` 选项只是向你表明可以传递任何名称的配置文件。这对于需要拆分成多个文件的复杂配置是非常有用的。

![image-20231106142012529](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311061420584.png)

### 使用

在配置了webpack.config.js之后,直接运行

```shell
webpack
```

即可

### 合并其他类型文件

在上面的代码中，我们主要是用webpack来处理我们写的js代码，并且webpack会自动处理js之间的相关的依赖。但是，在开发中我们不仅仅有基本的js代码处理，我们也需要加载css/图片，也包括一些高级的将ES6 转为ES5代码，将TypeScript转为ES5代码，将scss/less转为css,将.jsx、.vue文件转为js文件等等
对于webpack本身的能力来说，对于这些转化是不支持的；

**给webpack扩展对应的loader就可以了**

loader使用过程：

- 步骤一：通过npm安装需要使用的loader

- 步骤二：在webpack.config.js中的modules关键字下进行配置

#### 步骤-以css为例

在src目录下新建css文件夹，以及文件normal.css,修改一下上面的文件：（修改main.js里面的导入路径）

```css
body{
  /* background-color:rgb(36, 114, 118); */
  background-image: url('../img/girl.jpg');
}
```

main.js中添加导入css

```js
// 依赖css模块 
require('./css/normal.css')
```

**安装css-loader**

```shell
npm install --save-dev css-loader
```

**注意**,css-loader只负责将css文件进行加载,style-loader负责将样式添加到dom中

安装style-loader

```shell
npm install --save-dev style-loader
```

配置：webpack.config.js
**注意**：使用多个loader时，是从右向左使用

```js
const path = require('path');
//需要使用包  npm init生成package.json文件

module.exports = {
  entry: './src/main.js', //入口
  output: {
    path: path.resolve(__dirname, 'dist'), //动态获取路径,需要使用包path,   __dirname node中的全局变量
    filename: 'index.js'
  }, //出口
  module:{
    rules:[
      {
        // 正则表达式 匹配所有的css文件，css-
        test:/\.css$/,
        // css-loader只负责将css文件进行加载
        //style-loader负责将样式添加到DOM中
        //使用多个loader时，是从右向左使用
        use:['style-loader','css-loader']
      }
    ]
  }
}
```







