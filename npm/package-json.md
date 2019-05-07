### package.json文件解析

#### 1. package.json

npm在package.json文件中管理项目的依赖项以及项目的元数据。 node执行js中require（es6 import）的时候，也会根据package.json中的依赖项查找。

#### 2. package.json 文件解析

##### 2.1 作用

> - 描述了你的项目依赖哪些包，并指明依赖包的版本
> - 更好地与开发者分享，便于重复使用

##### 2.2 字段说明

2.2.1 文档链接 [package.json](https://link.juejin.im?target=http%3A%2F%2Fcaibaojian.com%2Fnpm%2Ffiles%2Fpackage.json.html)

2.2.2 必选字段

- name 全部小写，不支持空格
- version 版本号

2.2.3 可选字段

- description 描述信息，有助于npm search
- keywords 关键字，有助于npm search
- main 入口文件
- homepage 带协议前缀的URL，例如 [www.xxx.com](https://link.juejin.im?target=http%3A%2F%2Fwww.xxx.com)
- bugs 提供邮箱或url链接追踪项目的问题信息，例如

```
{
    "url":"http://github.com/issues,
    "email" :"xxx@gmail.com"
}
复制代码
```

- repository 当前的项目远程仓库，便于用户进行贡献，例如

```
"repository" :
  { 
    "type" : "git", 
    "url" : "http://github.com/npm/npm.git"
  }
复制代码
```

- author 用户，一个人，例如

```
{ 
    "name" : "Barney Rubble, 
    "email" : "b@rubble.com", 
    "url" : "http://barnyrubble.tumblr.com/"
}
复制代码
```

简写为

```
    "Barney Rubble <b@rubble.com>(http://barnyrubble.tumblr.com/)"
复制代码
```

- contributors 用户群组
- dependencies 项目的依赖安装包，指定版本，也可以是一个git url
- devDependencies 基本同于dependencies，但是如果只需要下载使用某些模块，而不下载这些模块的测试和文档框架，放在这个下面比较不错

#### 3. 依赖包的版本号

##### 3.1 基本的匹配模式

- version 完全匹配
- \>version 大于这个版本
- \>=version 大于或等于这个版本
- <version 小于这个版本
- <=version 小于等于这个版本
- version1 - version2，等价于 >=version1 && <=version2.
- range1 || range2 满足任意一个即可
- http://... Unix系统下使用的tarball的URL。
- git... Git地址
- latest 保持最新版本

上述列举条目与下面例子一一对应

```
{ 
    "dependencies" :
      { 
        "boo" : "2.0.1",
        "bar" : ">1.0.2", 
        "bar2" : ">=1.0.2", 
        "baz" : "<2.3.4", 
        "baz2" : "<=2.3.4", 
        "foo" : "1.0.0 - 2.9999.9999",
        "qux" : "<1.0.0 || >=2.3.1 <2.4.5“, 
        "asd" : "http://asdf.com/asdf.tar.gz", 
        'git-smt': "git address",
        "lat" : "latest",
      }
}
复制代码
```

##### 3.2 版本号

3.2.1 规范

- 补丁版本：解决了 bug 或者一些较小的更改，增加最后一位数字，比如 1.0.1 --> 1.0.2
- 小版本：增加了新特性，同时不会影响之前的版本，增加中间一位数字，比如 1.0.2 --> 1.1.2
- 大版本：大改版，无法兼容之前的，增加第一位数字，比如 1.1.2 --> 2.1.2

3.2.2 更新程度

- 如果只接受补丁版本的更新（也就是最后一位的改变），就可以这么写： 
  - 1.0
  - 1.0.x
  - ~1.0.4
- 如果接受小版本的更新（第二位的改变，接受补丁版本的改变），就可以这么写： 
  - 1
  - 1.x
  - ^1.0.4
- 如果可以接受大版本的更新（自然接受小版本和补丁版本的改变），就可以这么写： 
  - *
  - x
  - ""