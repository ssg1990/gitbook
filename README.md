# Promise

### 从callback和异步说起：

         JS是一门单线程语言，通过回调函数来实现异步和并发。由于回调函数的多层嵌套而造成代码的阅读性差和逻辑调理混乱被称之为回调地狱。为了解决回调地狱的问题，相继出现了promise和 await/async.

####  Promise   Callback 对比

1.promise更好的解耦

promise将context放在了promise内部，而具体的处理则放在了then中，连接仅靠参数传递，能够很好的做到解耦。

callback方式，直接传递callback handler。这使得具体callback失去了对具体事件处理调用的次数和时机权利

2.callback回调地狱

由于复杂的业务逻辑，常常需要嵌套的调用回调函数，使得代码逻辑混杂难以阅读和维护。

#### 什么是promise

future value , 约定

约定，一但定下立即生效，在未来的某个时间一定返回结果

future value, 结果产生在未来的某个时刻，且结果是未知的。

必然部分是立即执行和一定会有结果。 未知部分是产生结果的时间和结果的内容。

promise 构造函数： 

```javascript
Promise(function(resolve, reject){
      //resolve(v1)
      //reject(v2)
})
```

promise, then 与 thenable

promise的核心是then方法，then方法做的事情类似于回调函数做的事情。在完成promise函数体内部的任务后，通过resolve或者reject来触发

```javascript
let p = new Promise(function(resolve, reject){
    setTimeout(() => resolve(1), 300)
    reject(2)
}).then(result=>console.log(result), error=>console.log(error))
```

在一个promise中只能触发第一个resolve或者reject。resolve或者reject将会把value传递给then.resolve传递给第一个参数，reject传递个第二个参数.

当promise的函数体中出现run time exception时，会将runtime exception传递给then的第二个参数（或者catch\)

```javascript
let p = new Promise((resolve,reject) => {
    f()
}).catch(err => console.log(err))
```

catch函数可以直接获取reject传递的参数，可以把它当做then的一个特例。被catch或者then通过第二个参数捕获到的错误不会报出exception。同时，如果有没有被catch的reject的value，会以exception的方式来显示。



thenable 与 ducktype\(is a,  is like a\)

ducktype常用与弱类型语言的类型判断。强类型语言判断是否是一个种类，要判断他是否是这个类型。而对于弱类型语言只需要判断他像这个类型，又能做这个类型能做的事情。由此，引出了thenable对象。thenable是类型为Object或者function，其拥有then方法（包括其原型链上有then方法\)。

thenable按照ducktype的说法是可以当做promise的，但实际上他并不能作为promise。

例子\(from You Don't Know JS\)

```javascript
var p = {
	then: function(cb,errcb) {
		cb( 42 );
		errcb( "evil laugh" );
	}
};

p
.then(
	function fulfilled(val){
		console.log( val ); // 42
	},
	function rejected(err){
		// oops, shouldn't have run
		console.log( err ); // evil laugh
	}
);
```

可以作为resolve, reject的参数

1. value
2. thenable
3. promise

不同的处理， 

* value会被直接传给then
* thenable会被unwrap，并且会纠正thenable使之可以被当成promise接受
* 接受promise

```javascript
var thenable1 = {
    then: () => console.log(1)
}
new Promise((resolve, reject) => {
    resolve(thenable1)
}).then(() => {})   // out put 1

var thenable2 = {
    then:(cb, errcb) => {
        cb('resolve');
        errcb('reject');
    }
}
new Promise((resolve, reject) => {
    resolve(thenable2);
}).then(res => console.log(res), err => console.log(err))

```

new Promise.then的结果仍然是一个promise该promise 以then里面return 或者reject的值为状态

promise 3大状态 pending   rejected    resolved

Promise.resolve

Promise.reject

Promise.all

Promise.race

Promise.finally

Promise.done

