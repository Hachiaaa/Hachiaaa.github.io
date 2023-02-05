BOM

## 浏览器模型概述
1.代码嵌入网页的方法
* script标签
* 事件属性
* URL协议
> script 标签可以指定type属性，如果浏览器不认识，则不会加载其中的代码,但是依然存在于dom之中
> 网页元素的事件属性如onclick
> URL支持javascript: 协议，可以在URL的位置写入代码

2.浏览器渲染
渲染引擎处理网页分为四个阶段：
1. 解析代码：HTML解析为Dom, CSS解析为CSSOM
2. 对象合成，将DOM与CSSOM合并成渲染树
3. 布局，计算出渲染的布局
4. 绘制，将渲染树绘制到屏幕  

重流和重绘
> 页面生成以后，脚本操作和样式表操作，都会触发reflow和repaint。重流和重绘不一定一起发生，但是重流必然伴随着重绘，而重绘不一定需要重流。

javascript是一种解释型的语言，不需要编译，由解释器实时运行。但是缺点是系统开销大，运行速度慢。为了提高运行速度，目前的浏览器都把JavaScript代码进行一定程度的编译，生成类似字节码的中间代码，这种字节码就是运行在JavaScript引擎上。

# window对象
在浏览器里，window对象指的是浏览器窗口，也指代当前页面的顶层对象。  
1.window对象的重要属性
 1. opener,表示打开当前窗口的父窗口，如果没有（直接在地址栏输入），则返回null。通过opener属性可以获得父窗口的全局属性和方法
 2. frames, 返回一个类似数组对象，成员为页面内所有框架窗口，包括frame元素和iframe元素。
 3. devicePixelRatio, 返回一个数值，表示一个css像素与一个物理像素的大小之前的比率。
 4. screenX,screenY 返回浏览器窗口左上角相对于屏幕左上角的水平距离和垂直距离
 5. innerHeight，innerWidth, 返回网页在当前窗口的视口（viewport）大小。
 6. outerHeight,outerWidth,返回浏览器窗口的高度和宽度。包括菜单和边框
 7. scrollX,scrollY返回页面的水平滚动距离和垂直滚动距离

2.window对象的重要方法
1. alert(),prompt(),confirm()
> prompt弹出对话框，返回null或者用户输入的值
> confirm弹出对话框，返回一个布尔值。

2. open(),新建另一个浏览器窗口，类似于浏览器菜单的新建窗口，该方法会返回新窗口的引用。
3. close()，一般用来关闭window.open方法新建的窗口
4. moveTo()，moveBy()
5. resizeTo(),resizeBy()
6. scrollTo(),scrollBy()
7. foucs(),激活窗口，使其获得焦点。blur()，将焦点从窗口移除。
8. getComputedStyle(),接受一个元素节点作为参数，返回一个包含该元素的最终样式信息的对象。

3.事件
1. load事件，发生在文档在浏览器窗口加载完毕时
2. error事件，浏览器脚本发生错误时触发

## Navigator对象和Screen对象
window.navigator属性指向一个包含浏览器和系统信息的对象，脚本通过这个对象了解用户的环境信息。

Screen对象表示当前窗口所在的屏幕，提供显示设备的信息。
> * Screen.height,Screen.width 浏览器窗口所在屏幕的宽度和高度  
> * Screen.availHeight,Screen.availWidth 浏览器窗口的可用屏幕高度宽度，除去任务栏等。

## Cookie
1.cookie是服务器保存在浏览器的一小段信息，一般不超过4kb。浏览器每次请求服务器就会携带这段信息。
每个cookie一般有几个元数据：  
* cookie名字
* cookie值
* 到期时间
* 所属域名
* 生效路径

2.cookie由http协议生成，也主要在http协议使用。  
> 服务器如果希望在浏览器保存cookie，需要在HTTP回应头信息里放置一个 Set-Cookie字段。除了设置cookie值，还可以在Set-Cookie字段放置属性。

>如果服务器想要改变一个早先设置的cookie，需要同时满足四个条件。Cookie的key，domain，path，secure都匹配。不然会生成一个全新的cookie

> 浏览器向服务器发送http请求时，每个请求都会带上相应的cookie。主要使用HTTP头信息的cookie字段。  
cookie字段可以包含多个cookie

3.cookie的属性
* Expires,Max-Age   
Expires属性指定一个具体的到期时间，是UTC格式。浏览器根据本地的事件与该属性对比来决定cookie是否过期。  
Max-Age属性指定从现在开始cookie 存在的秒数。  
如果这两个属性同时指定，Max-Age优先，如果这两个属性都没有，则这个cookie就是一个Session Cookie，一旦用户关闭浏览器，浏览器就不会保存这个cookie。
* Domain，Path  
如果没有指定域名，默认为当前域名，且子域名不会附带这个cookie。如果指定，子域名会附带这个cookie。对于path属性，只要浏览器发现path属性是请求路径开头的一部分，就会在头信息里面带上这个cookie。
* Secure，HttpOnly  
Secure属性指定浏览器只有在加密协议HTTPS下才能将这个cookie发送到服务器。如果是http协议则服务器会自动忽略该属性。该属性不需要指定值。  
HttpOnly属性指定该cookie无法通过JavaScript脚本拿到。
* SameSite
> 用来限制第三方cookie，有三个值 Strict,Lax,None

4.document.cookie  
> 读取当前网页的所有cookie，前提是该cookie没有HTTPonly属性。该属性可写，可以通过它为当前网站添加cookie。但是该属性一次只能写入一个cookie，并且写入不是覆盖而是添加。就好像服务器设置cookie只能一行一行设置。也可以写入cookie属性用分号分割。

## XMLHttpRequest对象
> XMLHttpRequest对象主要用于脚本发出http请求，得到服务器数据，本身是一个构造函数。

```javascript
var xhr = new XMLHttpRequest();

xhr.onreadystatechange = function(){
  // 通信成功时，状态值为4
  if (xhr.readyState === 4){
    if (xhr.status === 200){
      console.log(xhr.responseText);
    } else {
      console.error(xhr.statusText);
    }
  }
};

xhr.onerror = function (e) {
  console.error(xhr.statusText);
};

xhr.open('GET', '/endpoint', true);
xhr.send(null);
````
1.实例属性
* readyState
  * 0表示实例已经生成，open方法未调用
  * 1表示open方法已调用，send方法未调用
  * 2表示send方法已调用，服务器头信息和状态码已收到
  * 3表示正在接收数据
  * 4表示数据接收完毕
* onreadystatechange 该属性指定一个监听函数
* response 表示服务器返回的数据体
* responseType 表示服务器返回的数据类型，该属性可写，告诉服务器返回指定类型的数据
* responseText 从服务器接收到的字符串
* status，statusText 状态码和状态提示文本
* withCredentials 一个布尔值，表示跨域请求用户信息（cookie和认证的HTTP头信息）是否会包含在请求之中。
* upload ，通过XMLHttpRequest发送文件后得到的一个对象，可以监听这个对象的各种事件来得知上传的进展。

2.实例方法
* open() 初始化XMLHttpRequest对象
* send() get请求不带参数，post请求带参数。 所有监听事件都必须定义在send方法之前。
* setRequestHeader()  在open之后send之前调用
* overrideMimeType()  用来指定MIME类型，覆盖服务器返回的真正MIME类型。
* getResponseHeader(), getAllResponseHeaders()分别表示获取指定的响应头和获取所有响应头

3.事件
* readyStateChange
* progress 文件上传
* load 服务器数据接收完毕
* error

## 同源限制
> 同源含义：协议，域名，端口相同  
同源限制的目的是保证用户信息安全。

>主要表现
> * 无法读取非同源网页的cookie，LocalStorage，IndexDB
> * 无法接触非同源网页DOM
> * 无法向非同源地址发送AJAX

1.对于完全不同源的网站，目前有两种方法，可以解决跨域窗口通信
* 片段识别符，父窗口把信息写入子窗口的hash路由，子窗口监听hashchange事件。
* postMessage，父窗口直接向子窗口发送信息，子窗口监听message事件。

2.AJAX规避同源限制的三种方法
* JSONP，主要利用了script标签可以跨域请求。
* WebSocket 一种双工通信协议
* CORS

## CORS
 > CORS全称是cross-origin resource sharing,跨域资源共享  
需要浏览器和服务器同时支持，目前所有浏览器支持该功能。  
CORS请求分为两类simple request，not-so-simple request

> 对于简单请求，浏览器直接发出，在头信息中增加一个Origin字段。  
如果origin域名在许可范围内，服务器返回的响应会多出几个头信息字段。
> * Access-Control-Allow-Origin  必须
> * Access-Control-Allow-Credentials 是否允许发送cookie
> * Access-Control-Expose-Headers 该字段可选。通常CORS请求，getResponseHeader()只能拿到6个服务器返回的字段，如果想拿到其他的必须在这个字段里指定。

> 对于非简单请求，会在正式请求之前，增加一次预检HTTP请求。  
除了Origin字段，预检请求的头信息还包括两个特殊字段Access-Control-Request-Method，Access-Control-Request-Headers 指定浏览器会额外发送的头信息

> 服务器回应的其他CORS相关字段如下：  
> * Access-Control-Allow-Methods
> * Access-Control-Allow-Headers
> * Access-Control-Max-Age 用来指代本次预检请求的有效期

## Storage接口
该接口主要用于脚本在浏览器保存数据。两个对象部署了这个接口，sessionStorage,localStorage。一个是用于一次会话，一个长期存在。
* 属性length
* 方法setItem(key,value)
* getItem()
* removeItem()
* clear() 清除所有保存的数据，返回undefined
* key() 接受一个整数作为参数，返回该位置对应的键值
* 该对象有一个storage事件，当接口储存数据发生变化的时候，会触发该事件。

## History对象
> 该对象表示当前窗口的浏览历史

* 两个属性length，state 第二个表示History堆栈最上层的状态值。
* 方法back(),forward(),go()
* pushState() 在历史中添加一条记录
* replaceState()
* popstate事件，每当history对象出现变化时，就会触发popstate事件。

## Location对象
> Location对象是浏览器原生提供的对象， 可以用来操作URL相关的信息。

一些重要方法
* assign() 接受一个参数，立即跳转到该网址
* replace() 效果与assign一致，不过replace会在浏览器历史中删除当前网址
* reload() 重新加载当前网址

## URL对象
> 网页的URL只能包含合法的字符，主要分为两类。元字符和语义字符。  
> 四个与编码解码相关的方法如下

* encodeURI() 转码整个URL，会将元字符和语义字符之外的所有字符都进行转义
* encodeURIComponent() 用于转码除了语义字符之外的所有字符，包括元字符
* decodeURI()
* decodeURIComponent()

> URL本身也是一个构造函数，可以用来构造，编码，解析URL

URL实例属性与Location对象几乎一致，静态方法如下
* createObjectURL() 用来为上传下载的文件生成一个URL字符串，代表了File或者Blob对象的URL
* revokeObjectURL() 用来释放上述方法生成的URL实例。它的参数就是上述方法返回的URL字符串

## URLSearchParams对象
> 该对象是浏览器原生提供的用来构造，解析和处理URL的查询字符串，本身也是一个构造函数。

## ArrayBuffer对象，Blob对象
> ArrayBuffer对象表示一段二进制数据，用来模拟内存里面的数据。  
Blob对象表示一个二进制文件的数据内容，全称是binary large object。  
Blob对象的两个实例属性是size，type。分别返回数据的大小和类型

# File，FileList，FileReader
> File对象代表一个文件，继承了Blob对象，有四个属性，size，type，name（文件名或文件路径），lastModified  
> FileList对象是一个类似数组对象，每个成员都是一个File实例。   
> FileReader对象用于读取File对象或者Blob对象所包含的文件内容。

FileReader的一些重要属性
* error
* readyState 读取文件时的当前状态，0表示尚未加载任何数据，1表示数据正在加载，2表示数据加载完成
* result 读取完成后的文件内容
* onload load事件的监听函数
* onerror error事件的监听函数

FileReader的一些重要方法
* readAsArrayBuffer()
* readAsBinaryString()
* readAsDataURL() 返回一个Data URL格式（base64编码）的字符串
* readAsText() 读取完成后，result属性将会返回文件内容的文本字符串。

## 表单与FormData对象
> 表单的每个控件会生成一个键值对，键名是name属性，键值是value属性。实际提交的时候，只要键值对不是合法的URL字符，浏览器会自动对其进行编码。

> FormData对象可以构造表单数据，用脚本来发送数据

> 表单的内置验证

* 自动校验，浏览器允许开发者指定一些条件，自动验证各个表单的值是否符合条件。如果一个表单控件通过验证，它就会匹配：valid的css伪类。相反，如果没有通过验证，它就会匹配：invalid的css伪类
* 手动校验的方法，checkValidity()
* 表单元素的novalidate属性可以关闭浏览器的自动校验。
* enctype属性。表单可以用四种编码，向服务器发送数据，编码格式由表单的enctype属性决定。
1. Get方法
2. application/x-www-form-urlencoded 默认值
3. text/plain 数据将以纯文本格式发送
4. multipart/form-data 数据将以混合格式发送，这种格式也是文件上传的格式。

## Web Worker
> js采用单线程模型，有很多的缺点。web worker就是为js创造多线程环境。





