python请求库requests常见api总结  

## 基本用法
1.`requests`可以直接跟上`post`，`get`，`put`，`delete`等方法，返回一个response对象   
2.`get`请求可以直接添加params参数用于构造get请求的参数,可以直接添加headers参数设置请求头。如果返回json格式，可以直接调用`response.json()`返回一个字典格式；如果返回字符串格式，直接调用`response.text`；如果返回的是二进制数据，直接调用`response.content` 
> 在get请求的时候，经常使用urllib.parse模块，利用该模块的urlencode方法构造params参数。  

3.`post`请求可以直接添加`data`参数传递post参数。  
4.响应的一些属性，`status_code`,`cookies`,`headers`分别表示状态码，cookies，响应头  

## 高级用法
1.文件上传，通过post方法的files参数。  
2.cookies的设置，可以直接写入headers请求头`Cookie`字段  
3.会话维持，如果想要两次请求共用cookie可以维持同一个会话。通过`requests.Session()`api.  

```python
import requests 
s = requests.Session() 
s.get('http://httpbin.org/cookies/set/number/123456789') 
r = s.get('http://httpbin.org/cookies')
print(r.text) 
```   
4.SSL证书验证。当发送HTTP请求的时候，它会检查SSL证书，可以使用verify参数控制是否检查此证书，默认为True.  
5.代理设置，通过proxies参数可以设置代理。  
6.超时设置，通过timeout参数设置。超过了这个时间无响应直接报错  
7.身份认证，可以用auth参数直接传递一个user和password的元组，requests会自动转换为HTTPBasicAuth类来认证。

