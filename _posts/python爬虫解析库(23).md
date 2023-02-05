xpath和BeautifulSoup介绍

## xpath和lxml库
1.通过lxml的etree模块构造xpath解析对象`etree.HTML()`  
2.xpath常用规则  
* nodename 选取此节点的所有子节点  
* / 从当前节点选择直接子节点
* // 从当前节点选取子孙节点
* .. 选取当前节点的父节点
* @ 选取属性

3.一些实例   
* `//li[@class='fuck']` 返回所有class为fuck的li节点  
* `//li[@class='fuck']//text()`  返回所有li子孙节点内部的所有文本，可能会夹杂一些特殊符号如换行符等  
* `//li/a/@href`返回所有li节点下所有a节点的href属性  
* 属性多值匹配，contains函数。`//li[contains(@class,'fuck')]/a/text()`  
* 多属性匹配，逻辑运算符。`//li[contains(@class,'fuck') and @name='guoqi']/a/text()`  
* 按序选择，`//li[1]/a/text()`,`//li[position()<3]/a/text()`  
* 节点轴的选择，`//li[1]/ancestor::*` , `//li[1]/attribute::*` , `//li[1]/descendant::span`。 第一个表示第一个li节点的所有祖先节点，第二个表示第一个li节点的所有属性，第三个表示第一个li节点的所有span子孙节点。

## Beautiful Soup  
1.beautiful soup在解析时依赖解析器，除了python标准库中内置的HTML解析器外，最常用的是lxml，可以解析HTML和XML。通过`soup=BeautifulSoup(markup,'lxml')`构造一个实例。  
2.基本用法  
* 节点选择器，直接调用节点名称，返回bs4.element.Tag类型，`soup.p`选择所有p节点，再调用string属性就可以获得节点的文本，调用attrs属性可以获得节点的所有属性列表。  
* 嵌套选择，bs4.element.Tag类型可以继续直接调用节点选择器。  
* 关联选择，可以调用contents获取节点元素的直接子节点,返回列表；如果调用children属性，返回同样的结果，不过返回类型是一个生成器。如果要获取子孙节点的话，可以使用descendants属性，返回类型也是一个生成器。
* 父节点和祖先节点，parent和parents。
* 兄弟节点，next_sibling和next_siblings;previous_sibling和previous_siblings。  

3.方法选择器  
* `find_all(name,attrs,recursive,text,**kwargs)`,查询所有符合条件的元素。name属性直接传递字符串，attrs属性传递一个字典，text参数用来匹配节点的文本，传入的形式可以是字符串或者正则表达式对象。  
* `find()`,跟find_all类似，不过返回的是第一个匹配的节点。  
* `select()`,css选择器。