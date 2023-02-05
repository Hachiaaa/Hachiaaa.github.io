js中的new和bind

## new操作符
> 1.创建了一个新对象obj  
> 2.将新对象的__proto__指向构造函数的原型  
> 3.将构造函数中的this绑定到obj  
> 4.如果构造函数返回的是对象，则直接返回该对象；如果返回的不是对象则忽略返回值，返回obj

```javascript
function ObjectFactory(){
    let obj={}
    let Constructor=[...arguments][0]
    let args=[...arguments].slice(1)
    obj.__proto__=Constructor.prototype
    let result=Constructor.apply(obj,args)
    return typeof result==='object'?result:obj
}
```

## bind绑定
> 1.将一个函数中的this绑定到指定对象上，返回一个函数  
> 2.参数可以在绑定时传，也可以在返回的函数中传，也可以在两者中都传递一部分  
> 3.返回的函数同样可以用作构造函数，这个时候回忽略里面的this绑定  

```javascript
Function.prototype.mybind=function(obj){
    let self=this
    let outerargs=[...arguments].slice(1)
    let fbind=function(){
        let innerargs=[...arguments]
        //如果是构造函数，this指向实例，self指向绑定函数
        //如果是普通函数，this指向window，self指向绑定函数
        self.apply(this instanceof self?this:obj,outerargs.concat(innerargs))
    }
    //当fbind为构造函数时，使其实例的原型链上有this.prototype;则上面的this instanceof self返回true
    function F(){}
    F.prototype=this.prototype
    fbind.prototype=new F()
    return fbind()
}
```