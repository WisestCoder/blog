### 普通的观察者模式与Rxjs中Observable的不同

**普通的Observer partten**

普通观察者模式是先添加订阅，再去发布执行；其内部存了一个订阅清单。
```javascript
class Producer {
	constructor() {
		this.listeners = [];
	}
	addListener(listener) {
		if(typeof listener === 'function') {
			this.listeners.push(listener)
		} else {
			throw new Error('listener 必須是 function')
		}
	}
	removeListener(listener) {
		this.listeners.splice(this.listeners.indexOf(listener), 1)
	}
	notify(message) {
		this.listeners.forEach(listener => {
			listener(message);
		})
	}
}

var egghead = new Producer(); 

function listener1(message) {
	console.log('listener1听到了：' + message);
}

function listener2(message) {
	console.log('listener2听到了：' + message);
}

egghead.addListener(listener1);
egghead.addListener(listener2);

egghead.notify('Hello, nihao')
```

**Rxjs中的observable**

Rxjs是先定义被观察者，再去添加观察者去订阅；observable中没有存储订阅清单，observable更像是去执行一个方法，并传入一些参数。
```javascript
var observable = Rx.Observable
	.create(function(observer) {
			observer.next('Jerry');
			observer.next('Anna');
			observer.complete();
			observer.next('not work');
	})
	
// 宣告一個觀察者，具備 next, error, complete 三個方法
var observer = {
	next: function(value) {
		console.log(value);
	},
	error: function(error) {
		console.log(error)
	},
	complete: function() {
		console.log('complete')
	}
}

// 用我們定義好的觀察者，來訂閱這個 observable	
observable.subscribe(observer)
```

**Rxjs的observable跟下面的代码同理**
```javascript
function subscribe(observer) {
		observer.next('Jerry');
		observer.next('Anna');
}

subscribe({
	next: function(value) {
		console.log(value);
	},
	error: function(error) {
		console.log(error)
	},
	complete: function() {
		console.log('complete')
	}
});
```

**訂閱一個 Observable 就像是執行一個 function**
