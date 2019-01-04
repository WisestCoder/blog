### 依赖注入

文章摘至[知乎专栏](https://zhuanlan.zhihu.com/p/53832991)，作者：大板栗

#### 背景
前端应用在不断壮大的过程中，内部模块间的依赖可能也会随之越来越复杂，模块间的 低复用性 导致应用 难以维护，不过我们可以借助计算机领域的一些优秀的编程理念来一定程度上解决这些问题，接下来要讲述的 IoC 就是其中之一。

#### 什么是IoC
`IoC` 的全称叫做 `Inversion of Control`，可翻译为为「控制反转」或「依赖倒置」，它主要包含了三个准则：
 1. 高层次的模块不应该依赖于低层次的模块，它们都应该依赖于抽象
 2. 抽象不应该依赖于具体实现，具体实现应该依赖于抽象
 3. 面向接口编程 而不要面向实现编程

概念总是抽象的，所以下面将以一个例子来解释上述的概念：

假设需要构建一款应用叫 App，它包含一个路由模块 Router 和一个页面监控模块 Track，一开始可能会这么实现：

```javascript
// app.js
import Router from './modules/Router';
import Track from './modules/Track';

class App {
    constructor(options) {
        this.options = options;
        this.router = new Router();
        this.track = new Track();

        this.init();
    }

    init() {
        window.addEventListener('DOMContentLoaded', () => {
            this.router.to('home');
            this.track.tracking();
            this.options.onReady();
        });
    }
}

// index.js
import App from 'path/to/App';
new App({
    onReady() {
        // do something here...
    },
});
```

嗯，看起来没什么问题，但是实际应用中需求是非常多变的，可能需要给路由新增功能（比如实现 `history` 模式）或者更新配置（启用 `history, new Router({ mode: 'history' })）`。这就不得不在 App 内部去修改这两个模块，这是一个 INNER BREAKING 的操作，而对于之前测试通过了的 App 来说，也必须重新测试。

很明显，这不是一个好的应用结构，高层次的模块 `App` 依赖了两个低层次的模块 `Router` 和 `Track`，对低层次模块的修改都会影响高层次的模块 App。那么如何解决这个问题呢，解决方案就是接下来要讲述的 `依赖注入（Dependency Injection）`。

#### 依赖注入

所谓的依赖注入，简单来说就是把高层模块所依赖的模块通过传参的方式把依赖「注入」到模块内部，上面的代码可以通过依赖注入的方式改造成如下方式：

```javascript
// app.js
class App {
    constructor(options) {
        this.options = options;
        this.router = options.router;
        this.track = options.track;

        this.init();
    }

    init() {
        window.addEventListener('DOMContentLoaded', () => {
            this.router.to('home');
            this.track.tracking();
            this.options.onReady();
        });
    }
}

// index.js
import App from 'path/to/App';
import Router from './modules/Router';
import Track from './modules/Track';

new App({
    router: new Router(),
    track: new Track(),
    onReady() {
        // do something here...
    },
});
```

可以看到，通过依赖注入解决了上面所说的 `INNER BREAKING` 的问题，可以直接在 `App` 外部对各个模块进行修改而不影响内部。

是不是就万事大吉了？理想很丰满，但现实却是很骨感的，没过两天产品就给你提了一个新需求，给 `App` 添加一个分享模块 `Share`。这样的话又回到了上面所提到的 `INNER BREAKING` 的问题上：你不得不对 `App` 模块进行修改加上一行 `this.share = options.share`，这明显不是我们所期望的。

虽然 `App` 通过依赖注入的方式在一定程度上解耦了与其他几个模块的依赖关系，但是还不够彻底，其中的 `this.router` 和 `this.track` 等属性其实都还是对「具体实现」的依赖，明显违背了 `IoC` 思想的准则，那如何进一步抽象 `App` 模块呢。

```javascript
class App {
    static modules = []
    constructor(options) {
        this.options = options;
        this.init();
    }
    init() {
        window.addEventListener('DOMContentLoaded', () => {
            this.initModules();
            this.options.onReady(this);
        });
    }
    static use(module) {
        Array.isArray(module) ? module.map(item => App.use(item)) : App.modules.push(module);
    }
    initModules() {
        App.modules.map(module => module.init && typeof module.init == 'function' && module.init(this));
    }
}
```

经过改造后 `App` 内已经没有「具体实现」了，看不到任何业务代码了，那么如何使用 `App` 来管理我们的依赖呢：

```javascript
// modules/Router.js
import Router from 'path/to/Router';
export default {
    init(app) {
        app.router = new Router(app.options.router);
        app.router.to('home');
    }
};
// modules/Track.js
import Track from 'path/to/Track';
export default {
    init(app) {
        app.track = new Track(app.options.track);
        app.track.tracking();
    }
};

// index.js
import App from 'path/to/App';
import Router from './modules/Router';
import Track from './modules/Track';

App.use([Router, Track]);

new App({
    router: {
        mode: 'history',
    },
    track: {
        // ...
    },
    onReady(app) {
        // app.options ...
    },
});
```

可以发现 `App` 模块在使用上也非常的方便，通过 `App.use()` 方法来「注入」依赖，在 `./modules/some-module.js` 中按照一定的「约定」去初始化相关配置，比如此时需要新增一个 `Share` 模块的话，无需到 `App` 内部去修改内容：

```javascript
// modules/Share.js
import Share from 'path/to/Share';
export default {
    init(app) {
        app.share = new Share();
        app.setShare = data => app.share.setShare(data);
    }
};

// index.js
App.use(Share);
new App({
    // ...
    onReady(app) {
        app.setShare({
            title: 'Hello IoC.',
            description: 'description here...',
            // some other data here...
        });
    }
});
```

直接在 `App` 外部去 `use` 这个 `Share` 模块即可，对模块的注入和配置极为方便。

那么在 `App` 内部到底做了哪些工作呢，首先从 `App.use` 方法说起：

```javascript
class App {
    static modules = []
    static use(module) {
        Array.isArray(module) ? module.map(item => App.use(item)) : App.modules.push(module);
    }
}
```

可以很清楚的发现，`App.use` 做了一件非常简单的事情，就是把依赖保存在了 `App.modules` 属性中，等待后续初始化模块的时候被调用。

接下来我们看一下模块初始化方法 `this.initModules()` 具体做了什么事情：

```javascript
class App {
    initModules() {
        App.modules.map(module => module.init && typeof module.init == 'function' && module.init(this));
    }
}
```

可以发现该方法同样做了一件非常简单的事情，就是遍历 `App.modules` 中所有的模块，判断模块是否包含 `init` 属性且该属性必须是一个函数，如果判断通过的话，该方法就会去执行模块的 `init` 方法并把 `App` 的实例 `this` 传入其中，以便在模块中引用它。

从这个方法中可以看出，要实现一个可以被 `App.use()` 的模块，就必须满足两个「约定」：
 1. 模块必须包含 `init` 属性
 2. `init` 必须是一个函数
 3. 这其实就是 `IoC` 思想中对「面向接口编程 而不要面向实现编程」这一准则的很好的体现。`App` 不关心模块具体实现了什么，只要满足对 接口 `init` 的「约定」就可以了。

此时回去看 `Router` 的模块的实现就可以很容易理解为什么要怎么写了：

```javascript
// modules/Router.js
import Router from 'path/to/Router';
export default {
    init(app) {
        app.router = new Router(app.options.router);
        app.router.to('home');
    }
};
```
#### 总结
`App` 模块此时应该称之为「容器」比较合适了，跟业务已经没有任何关系了，它仅仅只是提供了一些方法来辅助管理注入的依赖和控制模块如何执行。

控制反转（`Inversion of Control`）是一种「思想」，依赖注入（`Dependency Injection`）则是这一思想的一种具体「实现方式」，而这里的 `App` 则是辅助依赖管理的一个「容器」。
