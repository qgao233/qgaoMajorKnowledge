# promise

```
new Promise(function (resolve, reject) {  
    log('start new Promise...');  
    var timeOut = Math.random() * 2;  
    log('set timeout to: ' + timeOut + ' seconds.');  
    setTimeout(function () {  
	if (timeOut < 1) {  
	    log('call resolve()...');  
	    resolve('200 OK');  
	}  
	else {  
	    log('call reject()...');  
	    reject('timeout in ' + timeOut + ' seconds.');  
	}  
    }, timeOut * 1000);  
}).then(function (r) {  
    log('Done: ' + r);  
}).catch(function (reason) {  
    log('Failed: ' + reason);  
}); 
```
resolve函数的参数就是传到then中的函数的参数（回调函数）。
reject函数的参数就是传到catch中的函数的参数。

## 并行执行(结果有序)
```
var p1 = new Promise(function (resolve, reject) {  
    setTimeout(resolve, 500, 'P1');  
});  
var p2 = new Promise(function (resolve, reject) {  
    setTimeout(resolve, 600, 'P2');  
});  
// 同时执行p1和p2，并在它们都完成后执行then:  
Promise.all([p1, p2]).then(function (results) {  
    console.log(results); // 获得一个Array: ['P1', 'P2']  
}); 
```
## 并行执行(结果无序)

只取最先返回的结果：

```
var p1 = new Promise(function (resolve, reject) {  
    setTimeout(resolve, 500, 'P1');  
});  
var p2 = new Promise(function (resolve, reject) {  
    setTimeout(resolve, 600, 'P2');  
});  
Promise.race([p1, p2]).then(function (result) {  
    console.log(result); // 'P1'  
});  
```
