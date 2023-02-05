js常见考点

## js面试题汇总
1.跨域解决方案
* jsonp 主要利用了script标签请求资源不受同源策略限制。缺点是只能发起get请求
* cors 跨域资源共享
> cors分为简单请求和非简单请求  
> 简单请求get，post，head以及有限的头信息  
> 简单请求的步骤，浏览器发现是跨域请求，自动加上头信息origin，服务器设置通过Access-control-allow-origin设置哪些域名可以跨域访问  
> 非简单请求的步骤，浏览器先发出预检请求，头信息请求方法是OPTIONS，请求头信息还包括两个Access-Control-Request—Method, Access-Control-Request—Headers用来列出浏览器的cors请求会使用哪些方法以及哪些额外的头信息，供服务器检查。之后跟简单请求一样
* nginx反向代理
* postMessage

2.常见攻击
* csrf （cross-site-request-forgery）跨站请求伪造。攻击者盗用身份发送恶意请求。用户两个步骤分别是登录受信任网站A，并在本地生成cookie；在不登出A的情况下访问恶意网站B。
> 解决办法  
> * 表单加入一个hash字段，然后在服务端验证  
> * 验证码，每次提交表单需要携带验证码上的随机字符串。对用户不友好  
> * cookie的Httponly属性，放置cookie被脚本读取。  

* xss攻击 Cross-site scripting  跨站脚本注入

3.cookie和session
* cookie是存储在客户端，session存储在服务端
* cookie一般有大小限制，session的大小取决于硬件
* cookie的时效性一般较长，而session通常只存在于一次会话中  

4.http1.1与http1.0
* 缓存处理
* 带宽优化和网络连接的使用
* 错误通知的管理
* 长连接，默认开启了Connection：keep-alive

5.http2.0与http1.1
* 支持二进制传送，http1.x是字符串传送
* 多路复用
* 压缩算法压缩头部，减小传输体积
* 支持服务端推送  

6.缓存
* 缓存分为强缓存和协商缓存，强缓存不过服务器，协商缓存需要过服务器，协商缓存返回的状态码是304重定向。
* 强缓存Expires，使用绝对时间，可能会有误差
* 强缓存Cach-Control 
* 命中强缓存的表现形式，Firefox为一个灰色的200状态码，Chrome表现为200(from disk cashe)
* 协商缓存Last-Modified，Etag，If-Modified-Since，If-Match等字段

7.首屏加载优化方案
* 路由懒加载
* gzip压缩
* 服务端渲染SSR
* UI库按需加载

8.数据劫持（数据代理）
* defineProperty,vue的响应式原理  

9.Event loop 事件循环机制
* js是单线程（只有一个主线程），非阻塞（事件循环）的脚本语言
* Event loop主要分为主线程，宏任务（macrotask），微任务（microtask）
* 主线程包括js文件里可以直接在当前作用域执行的所有内容
* 宏任务包括setTimeout, setInterval, setImmediate（node独有）, I/O, UI render（浏览器独有），requestAnimationFrame（浏览器独有）
* 微任务包括， promise.then ,process.nextTick（node独有）
* 执行顺序为，主线程--->微任务--->宏队列中的一个任务--->微任务....依次循环， 在执行微任务的过程中如果又产生了微任务，那么会加入到队列的末尾，也会在这个周期被调用执行
* nodejs中的宏任务分成了六种队列，依次执行
* nodejs中的执行顺序为，主线程--->微任务--->第一个宏队列中的所有任务--->微任务....依次循环

10.js实现bind
```javascript
Function.prototype.bind = function (oThis) { if (typeof this !== "function") { 
      throw new TypeError("Function.prototype.bind - what is trying to be bound is not callable");
    } var aArgs = Array.prototype.slice.call(arguments, 1), 
        fToBind = this, 
        fNOP = function () {},
        fBound = function () { return fToBind.apply(this instanceof fNOP && oThis ? this : oThis || window,
                               aArgs.concat(Array.prototype.slice.call(arguments)));
        };

    fNOP.prototype = this.prototype;
    fBound.prototype = new fNOP(); return fBound;
  };
  ```
 11.防抖与节流
 > *  在进行窗口的resize，scroll等操作时，如果事件处理函数调用频率无限制，会增加浏览器的负担，导致用户体验糟糕。所以需要防抖（debounce）和节流（throttle）的方式来减少调用频率。  
 > * 防抖： 将几次操作合并为一次操作进行，原理是维护一个定时器，规定在delay时间后触发函数，如果在delay时间内触发的话，就会取消之前的定时器重新设置。所以只有最后一次操作能被触发  
 > * 节流： 使得一定时间内只触发一次函数。  
 
  简单防抖实现
   ```javascript
   function debounce(fn, wait) {
    var timeout = null;
    return function() { 
	    if(timeout !== null) 
		      clearTimeout(timeout);        
        timeout = setTimeout(fn, wait);    
    }
}
function handle() {    
    console.log(Math.random()); 
}
window.addEventListener('scroll', debounce(handle, 1000));
```   
简单节流实现
```javascript
var throttle = function(func, delay) {            
    var timer = null;            
    return function() {                
        var context = this;               
        var args = arguments;                
        if (!timer) {                    
            timer = setTimeout(function() {                        
                func.apply(context, args);                        
                timer = null;                    
            }, delay);                
        }            
    }        
}        
function handle() {            
    console.log(Math.random());        
}        
window.addEventListener('scroll', throttle(handle, 1000));
```    

12.commonjs中module.exports和exports的区别
模块最终返回的是module.exports,exports为指向module.exports的引用。

13.js实现new操作符
* 创建新的对象
* 将新对象的__proto__属性指向构造函数的prototype对象
* 使构造函数的this指向该对象
* 如果函数没有返回对象类型，new表达式的函数调用将会返回该对象的引用
```javascript
function New(func){
	let res={}
	if(func.prototype!==null){
		res.__proto__=func.prototype}
	let ret=func.apply(res,Array.prototype.slice.call(arguments,1))
	if((typeof ret==="object"||typeof ret==="function")&&ret !==null{
		return ret}
	return res
	}
```
14.js实现call,apply类似
```javascript
Function.prototype.mycall=function(content=window){
	content.fn=this
	let args=[...arguments].slice(1)
	let result=content.fn(...args)
	delete content.fn
	return result
	}
```
15.js实现继承
```javascript
function inherit(child,parent){
	function F(){}
	F.prototype=parent.prototype
	child.prototype=new F()
	child.protype.constructor=child}
```
16.函数柯里化
```javascript
function  curry(fn,args){
	let  length=fn.length
	var  args=args||[]
	return  function(){
		let  newargs=args.concat(Array.prototype.slice.call(arguments))
		if(newargs.length<length){
			return  curry.call(this,fn,newargs)
		}else{
			return  fn.apply(this,newargs)
			}
	}
}
function  multiFn(a,b,c){
console.log(a*b*c)
}
var  multi=curry(multiFn)
multi(2)(3)(4);
multi(2,3,4);
multi(2)(3,4);
multi(2,3)(4);
```
17.js实现promise
> promise A+规范
> * 三种状态 pending | fulfilled | rejected
> * 当处于pending状态时，可以转移到fulfilled或者rejected
> * 当处于fulfilled或者rejected状态时，就不可变

```javascript
function  myPromise(constructor){
	let  self=this
	self.status="pending"
	self.value=undefined
	self.reason=undefined
	function  resolve(value){
		if(self.status==="pending"){
			self.value=value
			self.status="resolved"
		}}
	function  reject(reason){
		if(self.status==="pending"){
			self.reason=reason	
			self.status="rejected"
		}}
	try{
		constructor(resolve,reject)
		}catch(e){
		reject(e)}
}
myPromise.prototype.then=function(onfulfilled,onrejected){
	let  self=this
	switch(self.status){
		case  "resolved":
			onfulfilled(self.value)
			break
		case  "rejected":
			onrejected(self.reason)
			break
		default:
}
}
```
18.js实现一个instanceof
```javascript
function instanceof(left,right){
	let proto=left.__proto__
	let prototype=right.prototype
	while(true){
		if(proto===null){
			return false}
		if(proto===prototype){
			return true}
		proto=proto.__proto__;
		}
	}
```
19.js实现深拷贝
```javascript
function deepcopy(obj){
	if(typeof obj==="object"){
		var result=obj.constructor===Array?[]:{}
		for(let i in obj){
			result=typeof obj[i]==="object"?deepcopy(obj[i]):obj[i]
			}
	}
	else{
		return obj
	}
	return result}			
```
20.构造函数的return
> 构造函数不需要显式地返回值。使用new来创建对象（调用构造函数）时，如果return的是非对象（包括null），会忽略返回值，如果return的是对象，则返回该对象。

21.数组的扁平化实现
es6实现
```javascript
function flatten(arr){
	while(arr.some(item=>Array.isArray(item)){
		arr=[].concat(...arr);}
	return arr;
	} 
```
递归实现
```javascript
function flatten(arr){
	let res=[]
	for(let i of arr){
		if(Array.isArray(i)){
			res=res.concat(flatten(i))}
		res.push(i)}
	return res}
```
22.数组去重
简单方法
```javascript
function unique(arr){
	let res=[]
	for(let i of arr){
		if(res.indexOf(i)==-1){
			res.push(i)}
		}
	return res
}
```
23.tcp发起连接和关闭连接（三次握手四次挥手）  
三次握手
* 发送端发送一个带有SYN（synchronize）标志的数据包给接收方
* 接收方接收后，回传一个带有SYN/ACK标志的数据包传递确认信息
* 最后，发送方再回传一个带有ACK标志的数据包，代表‘我知道你收到消息了’，握手结束  

四次挥手
* Client发送一个FIN,用来关闭Client到Server的数据传送，Client进入FIN_WAIT_1状态
* Server收到FIN后，发送一个ACK给client，Server进入CLOSE_WAIT状态
* Server发送一个FIN，用来关闭Server到Client的数据传送，Server进入LAST_ACK状态
* Client收到Fin后，进入TIME_WAIT状态，接着发送一个ACK给server，Server进入CLOSED状态

24.js垃圾回收机制
>一般来说没有被引用的对象就是垃圾，如果根对象访问不到，那么就会被垃圾回收机制清除  
>垃圾回收的内部算法是“标记-清除”，首先获取根元素然后标记所有来自他们的引用，以及儿子的引用，知道所有引用都可以从根元素访问。除了被标记的对象外，所有对象都被删除。  

25.js实现动画
> 将div平滑滑动，由快到慢。利用了浏览器requestAnimationFrame()函数，该函数接受一个回调函数，并传入时间戳给到这个回调，在下次重绘之前调用该函数。通常浏览器会在16ms重绘一次，刷新频率为60hz。  
```javascript
<!DOCTYPE html>
<html>
<head>
  <script>
    window.onload = () => {
      var element = document.getElementById('t');

      function step(timestamp) {
        console.log(timestamp)
        var progress = timestamp;
        element.style.left = Math.pow(progress / 5, .5) * 40 + 'px';
        if (progress < 5000) {
          window.requestAnimationFrame(step);
        } else {
          window.cancelAnimationFrame(step)
        }
      }

      window.requestAnimationFrame(step);
    }
  </script>
  <style>
    .container {
      position: relative;
      width: 1000px;
    }
    #t {
      position: absolute;
      width: 50px;
      height: 50px;
      background: red;
    }
  </style>
</head>

<body>
  <div class="container">
    <div id="t"></div>
  </div>
</body>
</html>
```
