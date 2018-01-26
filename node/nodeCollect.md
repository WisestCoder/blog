### node

小文件拷贝
```javascript
function copy(src, dst) {
    fs.writeFileSync(dst, fs.readFileSync(src));
}
```

大文件拷贝
```javascript
function copy(src, dst) {
    fs.createReadStream(src).pipe(fs.createWriteStream(dst));
}
```

只读数据流
```javascript
var rs = fs.createReadStream(src);

rs.on('data', function (chunk) {
    rs.pause();
    doSomething(chunk, function () {
        rs.resume();
    });
});

rs.on('end', function () {
    cleanUp();
});
```

只写数据流
```javascript
var rs = fs.createReadStream(src);
var ws = fs.createWriteStream(dst);

rs.on('data', function (chunk) {
    if (ws.write(chunk) === false) {
        rs.pause();
    }
});

rs.on('end', function () {
    ws.end();
});

ws.on('drain', function () {
    rs.resume();
});
```

理解异步IO模型

path.normalize

将传入的路径转换为标准路径，除了解析路径中的.与..外，还能去掉多余的斜杠。
```javascript
path.normalize('foo//baz//../bar')  =>  'foo/bar'
```

path.extname

获取文件扩展名
```javascript
path.extname('foo/bar.js')  =>  '.js'
path.parse(foo/bar.js).ext  =>  '.js'
```

判断一个路径是否存在
```javascript
function exists(pth, mode) {
  try {
    fs.accessSync(pth, mode);
    return true;
  } catch (e) {
    return false;
  }
}
```

递归遍历节点
```javascript
          A
         / \
        B   C
       / \   \
      D   E   F
```
遍历顺序为：A > B > D > E > C > F
```javacript
function travel(dir, callback) {
  fs.readdirSync(dir).forEach(function (file) {
    var pathname = path.join(dir, file);

    if (fs.statSync(pathname).isDirectory()) {
      travel(pathname, callback);
    } else {
      callback(pathname);
    }
  });
}
```

读取GBK文本文件 
```javascript
var iconv = require('iconv-lite');

function readGBKText(pathname) {
    var bin = fs.readFileSync(pathname);

    return iconv.decode(bin, 'gbk');
}
```

父子进程通信
```javascript
/* parent.js */
var child = child_process.spawn('node', [ 'child.js' ], {
        stdio: [ 0, 1, 2, 'ipc' ]
    });

child.on('message', function (msg) {
    console.log(msg);
});

child.send({ hello: 'hello' });

/* child.js */
process.on('message', function (msg) {
    msg.hello = msg.hello.toUpperCase();
    process.send(msg);
});
```

守护子进程
```javascript
/* daemon.js */
function spawn(mainModule) {
    var worker = child_process.spawn('node', [ mainModule ]);

    worker.on('exit', function (code) {
        if (code !== 0) {
            spawn(mainModule);
        }
    });
}

spawn('worker.js');
```

判断电脑cpu核数
```javascript
require('os').cpus().length
```

cluster模块允许设立一个主进程和若干个worker进程，由主进程监控和协调worker进程的运行。worker之间采用进程间通信交换消息，cluster模块内置一个负载均衡器，采用Round-robin算法协调各个worker进程之间的负载。运行时，所有新建立的链接都由主进程完成，然后主进程再把TCP连接分配给指定的worker进程。
```javascript
var cluster = require('cluster');

if(cluster.isMaster) {
  var numWorkers = require('os').cpus().length;
  console.log('Master cluster setting up ' + numWorkers + ' workers...');

  for(var i = 0; i < numWorkers; i++) {
    cluster.fork();
  }

  cluster.on('online', function(worker) {
    console.log('Worker ' + worker.process.pid + ' is online');
  });

  cluster.on('exit', function(worker, code, signal) {
    console.log('Worker ' + worker.process.pid + ' died with code: ' + code + ', and signal: ' + signal);
    console.log('Starting a new worker');
    cluster.fork();
  });
} else {
    http.createServer(function(req, res) {
    res.writeHead(200);
    res.end("hello world\n");
  }).listen(8000);
}
```

