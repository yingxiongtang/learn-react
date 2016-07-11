## 前言

本次培训主要涉及webpack,babel,react,redux和react-native,我会带着大家一步一步搭建一个react的项目，代码在https://github.com/yaliyingwy/learn-react，每一节都会有对应的tag,大家可以git checkout到对应tag查看该步骤的代码。
在此之前，假设你:

1. 了解es6的语法 (http://es6.ruanyifeng.com)
2. 安装了最新的nodejs(https://nodejs.org/en/download/)
3. 了解npm(http://www.ruanyifeng.com/blog/2016/01/npm-install.html)
4. 如果你想看到项目是怎么一步步搭建起来的，还需要了解git(http://rogerdudler.github.io/git-guide/index.zh.html)


由于我忽略了node_modules目录，代码clone下来后第一件事应该是npm install

## 为什么要用react全家桶？

### 提升开发效率

1. 理论上来说纯原生的app最强大，性能最好，能做任何炫酷的功能。
2. 但是现实的工作当中我们要面临快速迭代的需求，一个功能要开发ios，android，wap三套代码，移动端面临严重的碎片化。
3. 使用react或其他js框架开发的hybrid app可以一套代码统一整个移动端，但是因为性能的关系，只能做那种设计的比较朴素，偏向于纯展示的app
4. 使用react-native可以统一ios和android,因为react-native底层封装的是原生的界面，性能接近于原生app.
5. 相对于oc,swift和java来说，javascript语法更简单，更容易入门。
6. 无论app还是wap,采用相同的技术栈，代码复用程度高，没有技术上的沟壑。

### 基于flux或redux的架构，可维护性强

1. 类似于java的ssh,开发简单的项目可能像是把简单的事情搞复杂，但是代码逻辑清晰，项目越大，代码越多，越能体现优势。
2. react本身的组件化概念，非常容易积累可以复用的组件，目前react生态圈超级活跃，很容易找到一些现成的组件。如蚂蚁金服的ant.design(http://ant.design/docs/react/introduce),开袋即食。
3. 单向的数据流动，配和immutablejs，提高页面渲染效率

### 热更新

1. 绕过烦人的app store长时间审查，跟上需求的迭代速度
2. 经常让用户下载新版本，用户会烦，热更新的话在用户感知不到的情况下就能完成更新。

### js就是全栈型语言，积累了丰富的经验后，你完全可以独立开发网站，app,wap，甚至游戏

1. 关于nodejs优势和劣势的讨论https://www.zhihu.com/question/19653241
2. 哪些公司在用react? https://www.zhihu.com/question/26387853
3. 如何评价react-native? https://www.zhihu.com/question/27852694
4. react-native 一周年回顾 http://www.oschina.net/translate/react-native-a-year-in-review

## 工欲善其事，必先利其器，我们先来看看两个工具，webpack和babel

### babel (https://babeljs.io)
简单的来说，babel就是一个让你放心使用es6语法的工具，他会将es6语法编译成es5，解决浏览器的兼容问题
babel通过项目根目录下放置一个.babelrc的json文件进行配置，我们一般跟webpack搭配使用，这里就不单独说了。

### webpack (http://webpack.github.io/docs/)

webpack解决了javascript的模块化问题，从此再也不用区分前端js还是后端js,引用模块和第三方库的时候像其他语言一样import（或require）就可以了，工程化的组织，管理代码，告别以前混乱的时代。
同时webpack跟babel和react无缝的结合，现代的前端项目基本上离不开webpack。

webpack主要通过一个webpack.config.js的文件来配置（当然可以指定另外的名字），让我们一步一步来搭建一个基于webpack的工程。

1. 创建项目

    ```
    mkdir learn-react  创建一个项目目录
    cd learn-react && npm init 进入创建的目录，执行npm init，一路回车就可以了
    上面的命令执行完后会生成一个package.json的文件，该文件作用类似于pom,是每个nodejs项目的核心文件
    ```

2. 安装webpack和webpack-dev-server的依赖

    ```
    npm install webpack webpack-dev-server --save-dev --verbose 
    其中save-dev选项是安装完后将依赖写入package.json文件，verbose则是输出安装日志，不至于盯着屏幕没反应
    ```

3. 添加webpack.config.js文件, 并配置loader
    ```
    代码里想要正确的import或require代码，需要为webpack配置不同类型文件对应的loader
    我们loader的配置如下

    const babelQuery = {
      presets: ['babel-preset-es2015', 'babel-preset-react', 'babel-preset-stage-0'].map(require.resolve)
    };

    var loaders = [
      {
      test: /\.js$/,
      exclude: /node_modules/,
      loader: 'babel',
      query: babelQuery,
      }, {
      test: /\.json$/,
      loader: 'json-loader',
      }, {
      test: /\.woff(\?v=\d+\.\d+\.\d+)?$/,
      loader: 'url?limit=10000&minetype=application/font-woff',
      }, {
      test: /\.woff2(\?v=\d+\.\d+\.\d+)?$/,
      loader: 'url?limit=10000&minetype=application/font-woff',
      }, {
      test: /\.ttf(\?v=\d+\.\d+\.\d+)?$/,
      loader: 'url?limit=10000&minetype=application/octet-stream',
      }, {
      test: /\.eot(\?v=\d+\.\d+\.\d+)?$/,
      loader: 'file',
      }, {
      test: /\.svg(\?v=\d+\.\d+\.\d+)?$/,
      loader: 'url?limit=10000&minetype=image/svg+xml',
      }, { 
      test: /\.(jpe?g|png|gif|svg)$/i,
      loader: 'url?limit=10000!img?progressive=true'
      }, {
        test: /\.less$/,
        loader:  ExtractTextPlugin.extract('css?sourceMap!autoprefixer-loader!less?sourceMap')
      }
    ];

    上面的babelQuery是babel的配置。
    ExtractTextPlugin是生成单独的css文件，不然的话默认是没有css文件的，都内联在js里。
    其他loader的作用请自行google

    当然，光有了配置还是不行的，我们还需要安装对应的包
    npm install babel-loader url-loader img-loader json-loader css-loader less-loader autoprefixer-loader --save-dev --verbose 安装用到的loader
    npm install babel-preset-es2015 babel-preset-react babel-preset-stage-0 --save-dev --verbose  安装babel用到的组件
    npm install extract-text-webpack-plugin --save-dev --verbose 安装ExtractTextPlugin
    ```

4. 添加一些常用的plugin

    ```
    const plugins = [
      new webpack.optimize.CommonsChunkPlugin({
          name: 'common',
          filename: 'common.js'
      }),
      new ExtractTextPlugin("[name].css", {
          allChunks: true
      }),
      new webpack.NoErrorsPlugin(),
      new webpack.DefinePlugin({
        'process.env.DEBUG': !!process.env.DEBUG,
      }),
    ];

    if (!process.env.DEBUG) {
      plugins.push(
        new webpack.optimize.UglifyJsPlugin({
          output: {
            ascii_only: true
          },
          compress: {
            warnings: false
          }
        })
      );
    }

    Object.keys(entries).forEach((key) => {
      plugins.push(
        new HtmlWebpackPlugin({
          inject: true,
          minify: false,
          title: key,
          hash: true,
          filename: key + '.html',
          chunks: ['common', key],
          template: 'assets/tpl/index.html'
        })
      );
    });

    其中CommonsChunkPlugin是自动找出公用代码的
    ExtractTextPlugin上面已经说过了
    NoErrorsPlugin是处理错误的，开发的时候写错代码webpack-dev-server不会挂
    DefinePlugin为代码添加一些自定义的环境变量
    HtmlWebpackPlugin为每一个entry生成一个html,有多少个entry就要配多少个（entry后面单独讲)

    上面需要安装html-webpack-plugin，其他webpack都自带了
    npm install html-webpack-plugin --save-dev --verbse
    ```

5. 配置resolve

    ```
    const resolve = {
      root: __dirname,
      extensions: ['', '.js', '.less', '.woff', '.svg', '.ttf', '.eot', '.json', '.png', '.jpg'],
      modulesDirectories: ['.', 'node_modules'],
    };

    resolve的作用是指定支持的文件后缀和查找路径
    ```

6. 配置output

    ```
    const output = {
      path: path.join(__dirname, 'build'),
      publicPath: './',
      filename: '[name].js',
    };

    output主要是指定输出文件的配置
    ```

7. 配置entry

    ```
    const entries = {
      lifecycle: 'src/containers/lifecycle/lifecycle.js',
    }

    entry是webpack的核心配置，一个entry将会生成一个js
    这里我们随便写了两个js文件，用于测试。
    ```

8 为package.js 添加build和dist两个构建任务
    
    ```
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1",
      "build": "DEBUG=true node node_modules/webpack/bin/webpack.js",
      "dist": "DEBUG=false node node_modules/webpack/bin/webpack.js"
    },
    npm run build 和 npm run dist 分别试试，走你！
    ```


### webpack-dev-server

在上面的例子中，每改一点代码都要重新编译一次才能看到效果，而且随着文件增多，编译的过程很慢。
这里便轮到我们的开发神器webpack-dev-server登场了

前面我们已经安装过对应的包了，这里我们单独写一个js脚本来配置

1. 新建scripts目录，在下面新建一个dev.js文件

    ```
    const config = require('../webpack.config.js');
    const WebpackDevServer = require('webpack-dev-server');
    const webpack = require('webpack');

    Object.keys(config.entry).forEach((key) => {
      config.entry[key].unshift(require.resolve('webpack-dev-server/client') + '?http://localhost:8080', require.resolve('webpack/hot/dev-server'));
    });

    config.debug = true;

    config.devtool = 'eval-cheap-module-source-map';

    config.plugins.push(
      new webpack.HotModuleReplacementPlugin()
    );

    const devServerConfig = {
      contentBase: config.output.path,
      hot: true,
      historyApiFallback: true,
      stats: {
        colors: true
      }
    };

    new WebpackDevServer(webpack(config), devServerConfig)
      .listen(8080, 'localhost', (err) => {
        if (err) {
          console.error(err);
        }
      });

    整个脚本代码并不多，主要是往entry里面插入了两个热替换的脚本
    webpack.config.js中的entry也需要改成数组
    const entries = {
      lifecycle: ['src/containers/lifecycle/lifecycle.js'],
    }
    ```

2. 让我们在package.json中添加一条dev任务

    ```
    "dev": "DEBUG=true node scripts/dev.js"

    好了，npm run dev 然后随便改点代码试试
    ```
