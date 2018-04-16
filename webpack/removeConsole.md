### 打包时清除console

在项目开发调试过程中，我们经常会通过使用`console.log`去打印调试一些信息，却忘记删除，于是在打包的时候，console也会随着一起打包，这样不仅会增加文件大小，也可能会丢失一些重要信息，所以在打包的时候我们必须删除console。

#### UglifyJsPlugin中配置
```javascript
new UglifyJsPlugin({
  uglifyOptions: {
    compress: {
      warnings: false,
      drop_debugger: true,
      drop_console: true, // console
      pure_funcs: ['console'] // 移除console对代码的影响
    }
  },
  ...
})
```

#### 配置strip-loader
```javascript
{
  module: {
    loaders: [
      { test: /\.js$/, exclude: /node_modules/, loaders:['strip-loader?strip[]=console.log,strip[]=console.warn','babel'] },
    ]
  }
}
```

#### gulp下配置
```javascript
var gulp = require('gulp');
var stripDebug = require('gulp-strip-debug');

gulp.task('default', function () {
  return gulp.src('src/app.js')
    .pipe(stripDebug())
    .pipe(gulp.dest('dist'));
});
```

注意：这个库会同时删除console、 alert和debugger

#### babel配置
```javascript
// .babelrc中配置：
{
  "plugins": [ ["transform-remove-console", { "exclude": [ "error", "warn"] }] ]
}

// cli中使用：
babel --plugins transform-remove-console script.js

// node中使用
require("@babel/core").transform("code", {
  plugins: ["transform-remove-console"]
});

```
