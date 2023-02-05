vue响应式系统解析

# vue的执行过程

> 1.new Vue之后初始化声明周期函数，props，事件，data，computed，watcher等，在初始化的过程中，会对data里的属性递归式进行响应式处理。  
> 2.如果是运行时编译，存在template模板，则会对对应parse，optimize，generate三个阶段。parse解析模板字符串，optimize标记静态节点（优化diff过程），generate生成render function。  
> 3.render function渲染到视图的时候，会触发数据的gettter来进行依赖收集，将相应的watcher放进Dep依赖列表里。修改数据的时候，会触发setter来执行依赖列表里的watcher对应方法来更新视图。更新前会进行前后vDom对比，patch，最后只更新需要更新的地方


```javascript
function defineReactive(obj,key,val){
    let dep=new Dep()
    Object.defineProperty(obj,key,{
        configurable:true,//可以自定义get和set
        enumerable:true,//可以被遍历
        get(){
            dep.addSub(Dep.target)
            return val
        },
        set(newVal){
            if(newVal===val) return
            dep.notify()
        }
    })
}

//对整个对象进行响应式处理，递归的情况没有包含
function observe(obj){
    Object.keys(obj).forEach(key=>{
        defineReactive(obj,key,obj[key])
    })
}

//订阅者
class Watcher{
    constructor(){
        //将Dep.target指向当前的watcher对象
        Dep.target=this
    }
    update(){
        console.log('view has been updated')
    }
}
//发布者
class Dep{
    target=null
    constructor(){
        this.subs=[]
    }
    addSub(watcher){
        this.subs.push(watcher)
    }
    notify(){
        this.subs.forEach(watcher=>{
            watcher.update()
        })
    }
}

class Vue{
    constructor(options){
        this._data=options.data
        observe(this._data)
        //将Dep.target指向该对象
        new Watcher()
        //模拟getter依赖收集
        console.log('render~',this._data.x)
        console.log('render~',this._data.y)
    }
}

let testdata={x:1,y:2}

new Vue({
    data:testdata
})
//模拟setter通知watcher更新视图
testdata.x=3
testdata.y=4

// render~ 1
// render~ 2
// view has been updated
// view has been updated
```