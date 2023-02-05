DOM对象

## Node接口
* textContent 会解析html
* nextSibling
* previousSilbling
* childNodes
* parentNode
* parentElement
* appendChild()
* removeChild()
* insertBefore(new,refe)  在当前元素的父节点内部插入
* replaceChild()

## NodeList接口

## HTMLCollection接口

## ParentNode接口
* children  返回所有元素子节点
* append()
* prepend()

## ChildNode接口
* remove()
* before()
* after()
* replaceWith()

## Document节点
> 该节点继承了EventTarget接口和Node接口，并且混入了ParentNode接口

* open() 清除当前文档所有内容，并使其处于可写状态
* close()  关闭open()打开的文档
* write()
* 六种查询节点的方法
* 创建元素节点，文本节点，属性节点，注释节点的方法
* 添加和移除事件的方法，创建和触发事件的方法

## Element节点
* attributes
* className,classList  可读写
* innerHTML,outterHTML  可读写，不会解析html
* clientHeight,clientWidth  元素的css高度，不包括border，margin以及滚动条。可以通过document.documentElement获取视口高度宽度
* clientLeft,clientTop  元素左边框上边框的宽度
* scrollHeight,scrollWidth 元素总高度宽度，包括溢出容器的部分(比如overflow：hidden)，不包括border，margin以及滚动条。
* scrollLeft,scrollTop  元素滚动距离
* offsetParent 返回最靠近当前元素的、并且 CSS 的position属性不等于static的上层元素。
* offsetHeight,offsetWidth  比clientHeight和clientWidth多了border和滚动条(如果存在的话)
* offsetLeft,offsetTop  返回相对于offsetParent的位移
* style
* 四个查询节点的方法 （没有id，name查询）
* getBoundingClientRect()
* click()
* focus()
* blur()

## 属性操作
* Element.attributes
* Element.getAttribute()
* Element.getAttributeNames()
* Element.setAttribute()
* Element.removeAttribute()

## Text节点
* data
* wholeText
* nextElementSibling
* previousElementSibling

## DocumentFragment节点
> 通常用于批量插入，插入后节点本身会消失，只会插入DocumentFragment的子节点
```javascript
function reverse(n){
    var f=document.createDocumentFragment()
    while(n.lastChild){
        f.appendChild(n.lastChild)
    }
    n.appendChild(f)
}
```

上面这个例子用来反转一个list里的元素

## CSSStyleDeclaration接口
* cssText
* window.getComputedStyle()也返回一个CSSStyleDeclaration,可以获取伪元素。
* Element.style也返回一个CSSStyleDeclaration

## Mutation Observer
> Mutation Observer API 用来监视 DOM 变动。DOM 的任何变动，比如节点的增减、属性的变动、文本内容的变动，这个 API 都可以得到通知。概念上，它很接近事件，可以理解为 DOM 发生变动就会触发 Mutation Observer 事件。但是，它与事件有一个本质不同：事件是同步触发，也就是说，DOM 的变动立刻会触发相应的事件；Mutation Observer 则是异步触发，DOM 的变动并不会马上触发，而是要等到当前所有 DOM 操作都结束才触发。


