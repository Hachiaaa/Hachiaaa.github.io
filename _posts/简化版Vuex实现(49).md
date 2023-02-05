Vuex核心原理解析

> Vuex是一个状态管理工具，类似redux  
> Vuex有四个核心概念，state，getters，mutations，actions。getters类似vue里的计算属性，mutations是唯一可以修改state的地方，actions可以派发多个mutations，可以有异步操作，可定制化较高。  
> Vuex以插件形式发布，对外暴露一个install方法

```javascript
function forEachValue(obj,fn){
    Object.keys(obj).forEach(key=>fn(obj[key],key))
}

function registerGetter(store,getterName,getterFn){
    Object.defineProperty(store.getters,getterName,{
        //getter是只读属性
        get:()=>{
            return getterFn(store.state)
        }
    })
}

function registerAction(store,actionName,actionFn){
    store.actions[actionName]=()=>{
        actionFn.call(store,store)
    }
}

function registerMutation(store,mutationName,mutationFn){
    store.mutations[mutationName]=()=>{
        mutationFn.call(store,store.state)
    }
}


class Store{
    constructor(options={},Vue){
        //store对象的options(包括state，getters，mutations，actions)
        this.options=options
        this.getters={}
        this.mutations={}
        this.actions={}
        //注册每一个getter
        forEachValue(options.getters,(getterFn,getterName)=>{
            registerGetter(this,getterName,getterFn)
        })
        //注册每一个mutation
        forEachValue(options.mutations,(mutationFn,mutationName)=>{
            registerMutation(this,mutationName,mutationFn)
        })
        //注册每一个action
        forEachValue(options.actions,(actionFn,actionName)=>{
            registerAction(this,actionName,actionFn)
        })
        //将state的数据放到vue响应式里，如果有数据的变更会触发视图render
        this._vm=new Vue({
            data:{
                state:options.state
            }
        })
    }
    //对外是只读属性且是响应式
    get state(){
        return this._vm.$data.state
    }
    commit(type){
        this.mutations[type]()
    }
    dispatch(type){
        return this.actions[type]()
    }
}
//对外暴露install方法，注册一个全局的mixin
//在beforeCreate钩子中注册$store属性，使得所有组件都能拿到$store
function install(Vue){
    Vue.mixin({
        beforeCreate(){
            const options=this.$options
            if(options.store){
                //组件内部有store则优先使用组件内部的store
                this.$store=options.store
            }else if(options.parent && options.parent.$store){
                //组件内部没有store，从根组件继承
                this.$store=options.parent.$store
            }
        }
    })
}
//导出
export default{
    Store,
    install
}
//使用
Vue.use(Vuex)
new Vuex.Store({
    state:{},
    getters:{},
    mutations:{},
    actions:{}
})
```