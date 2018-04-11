## Webpack2引入的Tree Shaking技术

Tree Shaking是`Rollup.js`的作者提出来的，换句话说就是消除 unused code。

这个功能能够通过基于ES6 modules的静态特性做检测来找出未使用的代码。然后配合uglifyjs把无用代码“筛选”掉。

代码地址：[webpack2-tree-shaking](https://github.com/wisestcoder/webpack2-tree-shaking)

比如在如下场景下：
```javascript
util.js

export const foo = function () {
    return 1
}

export const bar = function () {
    return 2
}

export const foobar = function () {
    return 3
}
bar.js

import {bar} from './util'
const a = bar();
```

我们使用foo.js作为入口文件，自然希望webpack编译后只把util.js里面的bar变量引入到最终生成的文件，而其他没有用掉的能够被“优化”掉。Tree Shaking就是为了解决上述问题。

最通常的情况就是我们项目中有某个公用方法文件，但是可能不同入口文件能够使用的方法很有限，这样会产生很多无用的方法打包到最后的文件当中。Tree Shaking则能够移除由此产生的大量冗余代码，达到优化的目的。

`npm run build`后输出如下：

![](https://github.com/wisestcoder/blog/blob/master/webpack/images/treeShaking.png)

我们再输入`npm run build2`对代码进行压缩，发现所有标记了unused的代码段全部舍弃了。