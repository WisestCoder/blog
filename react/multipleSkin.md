### react实现多皮肤管理

要实现换肤功能，目标就是打包生成多份皮肤文件，需要哪个就用哪个。

以下围绕webpack来配置，项目目录如下：
```javascript
react-skin
├── dist
│   ├── app-en.js
│   ├── app-zh-cn.js
│   ├── app.css
│   ├── demo-en.js
│   ├── demo-zh-cn.js
│   ├── demo.css
│   ├── demo.html
│   ├── green
│   │   └── home.css
│   ├── home-en.js
│   ├── home-zh-cn.js
│   ├── home.css
│   ├── index.html
│   └── red
│       └── home.css
├── html
│   ├── demo.html
│   └── index.html
├── node_modules
├── package.json
├── src
│   ├── app
│   │   ├── app.js
│   │   ├── app.less
│   │   ├── db.js
│   │   ├── theme
│   │   │   ├── default.less    -> 配置默认的皮肤样式
│   │   │   ├── green.less      -> 配置绿色的皮肤样式
│   │   │   └── red.less        -> 配置红色的皮肤样式
│   │   ├── util.js
│   │   └── variables.js
│   ├── components
│   │   ├── search-data
│   │   │   ├── SearchData.jsx
│   │   │   └── index.js
│   │   └── search-word
│   │       ├── SearchWord.jsx
│   │       └── index.js
│   ├── i18n
│   │   ├── en.js
│   │   ├── index.js
│   │   └── zh-cn.js
│   └── pages
│       ├── demo
│       │   ├── PageDemo.jsx
│       │   ├── PageDemo.less
│       │   ├── index.js
│       │   └── logic.js
│       └── home
│           ├── PageHome.jsx
│           ├── PageHome.less
│           ├── index.green.js  -> 新增的皮肤入口
│           ├── index.js
│           ├── index.red.js    -> 新增的皮肤入口
│           ├── logic.js
│           └── page-styles
│               ├── default.less   -> 引入了皮肤的less和页面的less
│               ├── green.less     -> 引入了皮肤的less和页面的less
│               └── red.less       -> 引入了皮肤的less和页面的less
└── webpack.config.js
```

#### 1. 打包多份皮肤文件
我们要想生成多份css文件，就需要在入口配置更多的入口，我们做如下设计：

首先，我们需要在项目目录下创建新的入口：
![](https://github.com/wisestcoder/blog/blob/master/react/images/skin01.png)

然后我们在webpack中新增带皮肤配置的入口：
```javascript
module.exports = function(config) {
  // 读取每一个 entry 文件，查找是否 path/index.(xxx).js 括号中的关键词文件存在
  const entry = config.entry;
  const addedEntry = {};
  Object.keys(entry).forEach(function(key) {
    const fileName = entry[key];
    const dirName = path.parse(fileName).dir;
    fs.readdirSync(dirName).forEach(function(file) {
      if (/index\..*\.js/.test(file)) {
        const theme = /index\.(.*)\.js/.exec(file)[1];
        addedEntry[`${theme}/${key}`] = `${dirName}/${file}`;
      }
    });
  });

  config.entry = Object.assign({}, entry, addedEntry);
};
```

#### 控制不同的皮肤样式
观察最开始列出来的项目结构图，我们一步步分析：

1. 我们在app文件夹下，新建一个theme文件夹，来放置皮肤的less
2. 我们在不同的less文件，如`app/theme/green.less`中定义不同的颜色变量，如下图：
![](https://github.com/wisestcoder/blog/blob/master/react/images/skin02.png)
3. 我们在`page/home`文件夹（也就是我们每个页面所对应的文件夹）下，新建一个`page-styles`文件夹，里面放置了不同的less文件，每个文件都引用了皮肤的less和页面的less，如图：
![](https://github.com/wisestcoder/blog/blob/master/react/images/skin03.png)
4. 我们在打包之后，就能成功的打包出带样式的css文件了，如下图：
![](https://github.com/wisestcoder/blog/blob/master/react/images/skin04.png)

#### 删除多余的js文件
我们观察到上图我们打包之后，会打包一些多余的js，其实我们只需要css，而不需要js，因此我们最后一步需要删除多余的js文件，有以下两种方法：
1. 我们可以自己写一段node脚本，去执行删除的任务，代码如下：
```javascript
const fs = require('fs');

fs.readdirSync('./dist').forEach(function(file) {
  fs.stat(`./dist/${file}`, function(err, stats) {
    if (stats.isDirectory ()) {
      fs.readdirSync(`./dist/${file}`).forEach(function(item) {
        if (/.*\.js$/.test(item)) fs.unlinkSync(`./dist/${file}/${item}`);
      })
    }
  })
});
```
2. 我们编写一个webpack的插件，代码如下：
```javascript
const fs = require('fs');
const process = require('process');

function cleanThemeJsWebpackPlugin() {};

cleanThemeJsWebpackPlugin.prototype.apply = function(compiler) {
  compiler.plugin('done', function(compiler) {
    console.log("正在删除theme中的js...");
    const myPath = process.cwd();
    fs.readdirSync(`${myPath}/dist`).forEach(function(file) {
      fs.stat(`${myPath}/dist/${file}`, function(err, stats) {
        if (stats.isDirectory ()) {
          fs.readdirSync(`${myPath}/dist/${file}`).forEach(function(item) {
            if (/.*\.js$/.test(item)) fs.unlinkSync(`${myPath}/dist/${file}/${item}`);
          })
        }
      })
    });
    console.log("done");
  });
};
```

以上代码就能实现一个简单的多皮肤配置，代码写的很乱，多多包涵~

最后附上项目地址：[https://github.com/WisestCoder/react-skin/tree/master](https://github.com/WisestCoder/react-skin/tree/master)