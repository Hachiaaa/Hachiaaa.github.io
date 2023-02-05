BOM梳理

## 概述
代码嵌入网页的方法
* script直接写入, 这个标签有一个type属性一般为`application/javascript`,如果type改成了浏览器不认识的字段，那么这行代码不会执行。但是标签仍然存在于dom中
* script外部引入,如果引入的脚本有非英语字符应该用`charset`注明编码,还允许设置一个`integrity`属性写入外部脚本的hash签名(src属性的hash值),如果src属性被篡改则浏览器不会加载脚本
* 事件属性 如`onclick='console.log("fuck")'`
* URL协议 可以在url的位置写入代码,包括浏览器的地址栏。 如果代码返回一个字符串，浏览器就会新建一个文档，展示这个字符串的内容，原有文档内容会消失

script元素工作原理
* 浏览器一边下载html一边解析
* 遇到script就暂停解析，把网页渲染权交给js引擎
* 如果script引用了外部脚本，则先下载脚本再执行，否则直接执行其中代码
* js执行完毕，控制权交还渲染引擎

存在的问题以及应对措施
* 如果外部脚本加载时间很长，则浏览器会一直等待，处理办法是把script标签放在底部，或者绑定script标签的load事件
* 如果脚本加载时调用了还没解析完成的dom节点会报错，解决办法是添加DOMContentLoaded事件的回调函数
* 如果有多个script标签，浏览器会并行下载，但是执行时会按照先后顺序执行
* css加载不会阻塞DOM树的解析，但是会阻塞DOM树的渲染
* 对于来自于同一个域名的资源，同时最多下载6-20个资源，即最多同时打开的TCP连接有限制。
* defer属性延迟脚本执行，等到DOM加载完成后再执行，对于动态生成的script标签，defer属性不起作用
* async属性异步下载脚本，下载完成就执行。所以无法保证script标签的执行顺序，谁先下载完成谁就执行。
* 加载使用的协议默认是http协议
* 浏览器的核心： 渲染引擎和js执行引擎

渲染的步骤：
1. 解析HTML为DOM,解析css为CSSOM  
2. 将DOM和CSSOM合成为渲染树
3. 计算布局  
4. 绘制

> 以上步骤并非严格按顺序执行,有可能第一步还没完成就开始第二步和第三步

重流重绘：重流必然导致重绘，重绘不一定需要重流。其中重要的优化就是将代码放入`window.requestAnimationFrame(callback)`,表示在下一次重绘之前执行回调函数,通常是每秒60次。

js引擎：
* 解释型语言，不需要编译，由解释器实时运行。缺点是每次运行都要调用解释器，系统开销大，运行速度慢。
* 一般js引擎会把代码整理成AST抽象语法树，然后翻译成中间字节码，再解释执行字节码。
* 现代浏览器一般采用即时编译，即字节码只在运行时编译，用到哪一行就编译到哪一行，并且缓存编译结果。
* 字节码一般运行在虚拟机上。

## Window对象
* alert(),confirm(),prompt()
* open(),close()
* scrollTo(),scrollBy()
* getSelection(),得到一个Selection对象表示用户现在选中的文本
* getComputedStyle()
* requestAnimationFrame()
* requestIdleCallback(callback)  将回调推迟到系统资源空闲时执行。


## Navigator对象和Screen对象
* Screen.height,Screen.width
* Screen.availHeight,Screen.availWidth  除去了某些不可用的部分，比如系统任务栏

## Cookie
cookie的元信息
* 名字
* 值
* 到期时间
* 所属域名
* 生效路径  只有在访问该路径及其子路径才有效

> cookie是服务器保存在浏览器的一段信息，一般为4kb,用户可以设置浏览器不接受cookie,也可以设置不向服务器发送cookie,cookie可以通过`document.cookie`读写 

* cookie生成由服务器响应头`Set-Cookie`生成，可以包含多个属性没有次序要求，中间用分号隔开
* 如果服务器想要更改Cookie,必须保证key,domain,path,secure都匹配否则会生成一个新的cookie
* 浏览器请求的时候会自动携带cookie,一般在Cookie字段里面,`Cookie:name=value1;name=value2`,多个Cookie用分号隔开,浏览器收到cookie并不能确定Cookie的过期时间

Cookie的属性
* Expires,Max-Age 优先级后者高于前者,前者表示一个具体的UTC时间,后者表示从现在开始的秒数。值得注意的是如果Set-cookie没有这两个属性，那么这个cookie是一个session cookie
* Domain,path  如果没有指定Domain,浏览器请求时默认为当前域名且子域名不会附带这个Cookie。
* Secure,HttpOnly Secure表示浏览器只能通过https协议发送cookie到客户端，httponly属性指定该cookie无法被脚本读取到
* SameSite,用来防止CSRF攻击，该属性有三个等级。
1.strict 完全禁止第三方cookie
2.Lax 除了导航到目标网址的get请求(链接,预加载请求,GET表单)，其余均不发送cookie。谷歌浏览器一般是Lax为默认值
3.None 

## XMLHttpRequest对象
> 用脚本发起http请求,XMLHttpRequest是主要接口

* readyState
* onreadystatechange
* response
* responseType
* withCredentials 跨域请求是否携带cookie
* open()
* send()
* setRequestHeader()

## 同源限制
> 协议相同，域名相同，端口相同

一般非同源有如下限制
* 无法共享cookie,localStorage,IndexDB
* 无法接触DOM
* 无法发送AJAX

> 跨窗口通信,postMessage()接口

Ajax同源限制的规避:
* JSONP
* WebSocket
* CORS

## CORS
CORS请求分为两类,简单请求和非简单请求
> 简单请求包括HEAD,POST,GET 并且头信息只有指定的几个。简单请求会自动在请求头添加`Origin`字段，如果在许可范围内，服务器返回多出几个头信息，其中最重要的是`Access-Control-Allow-Origin`  

> 非简单请求会先发出一个预检请求，询问服务器当前域名是否在许可名单之内以及可以使用的请求方法和请求头字段，得到肯定答复才会正式发起AJAX请求。预检请求的方法是OPTIONS,通过预检请求后每次请求就跟简单请求一样。

## Storage接口
* setItem()
* getItem()
* removeItem()
* clear()

## History接口
* back()
* forward()
* go()
* pushState() 在历史中添加新纪录
* replaceState() 修改当前记录
* popstate事件,只有用户点击了倒退前进才会触发。
* 后三个API是前端路由的底层实现(改变url不刷新,检测url改变) 

## URL对象
全局的编码解码方法
* encodeURI()
* decodeURI()
* encodeURIComponent()
* decodeURIComponent()

## ArrayBuffer和Blob对象

## File,FileList,FileReader

## FormData对象
> 通过脚本完成表单的提交

表单的enctype
* application/x-www-form-urlencoded
* text/plain
* multipart/form-data 文件上传的格式







