html5新特性

## 声明
* 需要显式指定<DOCTYPE !html>

## 标签
* 增加语义化标签,`header,footer,aside,section,article,nav`等  
* 删掉了一些标签  
* 增加了基于媒体的`audio,video`标签  

## 存储
* 增加了`sessionStorage`,`localStorage`  
* 增加IndexedDB和Web SQL,前者是NoSQL,后者更像是关系型数据库，现在已经被废弃  
* 增加了应用程序缓存器application cache，在底层支持了PWA技术的发展  

## 属性
* 表单的type属性增加了很多，比如日期相关的，url，email等  
* 其他标签新增属性，比如`meta的charset`，`script的async`  
* 使得部分属性名默认具有Boolen属性为true.可以直接写属性就可以，比如`input的checked`

## meta标签
* charset属性
* name+content,定义一些元信息，比如作者，网址，seo关键字，版权，网站描述等信息  
* http-equiv，定义关于网络的信息，比如浏览器的刷新，资源过期信息等  

## src和href
* src（source)表示资源下载到哪里去，href(hypertext reference)表示资源指向的位置  
* href指向的资源会在文档解析过程中并行加载，不会阻塞对当前文档的处理，所以css资源建议用link标签加载，而不是用`@import`,后者会导致文档解析被挂起。  
* 当浏览器解析到src时，会暂停其他资源的加载，知道将该资源加载执行完毕，才继续解析原文档。所以建议将script标签放在最后。

## defer和async
* script的defer和async属性都表示文档和脚本异步加载  
* defer会让脚本在文档加载完毕之后（触发DOMContentLoaded事件）执行。 
* async在脚本解析完毕后，会挂起html文档的解析，执行脚本后，再继续解析html文档
* 当defer和async同时存在时，async的优先级高于defer