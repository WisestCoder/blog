### Runtime transform转换器

[官方文档中文版](https://segmentfault.com/a/1190000009065987)

**为什么使用**
- babel对一些函数进行编译时，会添加一些辅助来确保你的功能可以正常进行，默认情况下，这些辅助会出现在你每个使用了的文件中，这会导致编译重复。

- 这个转换器为你的代码创建了一个沙盒环境。如果你使用`babel-polyfill`并且把它内置提供`promise`,`set`,`map`这样的对象或功能，他们将会污染全局环境。也许在一个应用中或者命令行工具中没问题，但是如果你的代码是个库，你想发布给其他人使用，因为使用的人可能在各种各样的执行环境中，所以可能导致错误，不能执行。

**runtime转换器插件主要做了三件事**
- 当你使用generators/async方法、函数时自动调用`babel-runtime/regenerator`

- 当你使用ES6 的Map或者内置的东西时自动调用`babel-runtime/core-js`

- 移除内联babel helpers并替换使用`babel-runtime/helpers`来替换