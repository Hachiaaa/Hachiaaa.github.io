---
layout: post
title: "如何实现一个简化版vue-router"
date: 2020-02-01
sitemap: false
keywords: "blog"
description: "🚀"
---

vue router核心原理解析

> 前端路由的两种方式。第一种是hash路由，通过监听`hashchange`事件来渲染页面；第二种是history路由，通过pushState和replaceState方法可以在不刷新页面的前提下改变路由

## 首先定义基本的路由类
```javascript
//history对象
class HistoryRoute{
    constructor(){
        this.current=null
    }
}
//history的pushState方法和replaceState方法
function pushState(url,replace=false){
    const history=window.history
    if(replace){
        history.replaceState({key:history.state.key},'',url)
    }else{
        history.pushState({key:Date.now()},'',url)
    }
}
class vueRoter{
    constructor(options){
        //history和hash两种模式
        this.mode=options.mode || 'hash'
        //路由列表
        this.routes=options.routes || []
        //path到component的映射map
        this.routeMap=this.createMap(this.routes)
        //定义history对象
        this.history = new HistoryRoute()
        this.init()
    }
    //针对路由添加hashchange事件和popstate事件
    init(){
        if(this.mode==='hash'){
            location.hash?'':location.hash='/'
            window.addEventListener('load',()=>{
                this.history.current=location.hash.slice(1)
            })
            window.addEventListener('hashchange',()=>{
                this.history.current=location.hash.slice(1)
            })
        }else{
            window.addEventListener('load',()=>{
                this.history.current=location.pathname
            })
            window.addEventListener('popstate',()=>{
                this.history.current=location.pathname
            })
        }
    }
    //路由映射表
    createMap(routes){
        return routes.reduce((a,b)=>{
            a[b.path]=b.component
            return a
        },{})
    }
    push(url){
        if(this.mode==='hash'){
            location.hash=url
        }else{
            pushState(url)
            //history.pushState和history.replaceState不会触发popstate事件
            //需要手动通知view-router $route发生了改变
            this.history.current=location.pathname
        }
    }
    replace(url){
        if(this.mode==='hash'){
            location.hash=url
        }else{
            pushState(url,true)
            //history.pushState和history.replaceState不会触发popstate事件
            //需要手动通知view-router $route发生了改变
            this.history.current=location.pathname
        }
    }
}
```

## 然后vueRouter对外暴露一个install方法以插件的形式安装

```javascript
vueRouter.install=function(Vue){
    Vue.mixin({
        beforeCreate(){
            //根组件并且有router对象
            if(this.$options && this.$options.router){
                this._root=this
                this._router=this.$options.router
                //定义了响应式属性，当路由对象改变的时候，会触发页面render
                Vue.util.defineReactive(this,'xxx',this._router.history)
            }else{
                //如果不是根组件，则一直往上找
                this._root=this.$parent._root
            }
        }
        //对所有组件暴露两个属性$router和$route
        Object.defineProperty(this,'$router',{
                get(){
                    return this._root._router
                }
            })
        Object.defineProperty(this,'$route',{
                get(){
                    return this._root._router.history.current
                }
            })
    })
    //注册全局组件router-view
    Vue.component('router-view',{
        render(h)=>{
            let current=this.$route //当前路由
            let routeMap=this.$router.routeMap  //拿到路由映射表
            return h(routeMap[current]) //直接渲染
        }
    })
    //注册全局组件router-link
    Vue.component('router-link',{
        props:{
            to:{
                type:[Object,String],
                required:true
            },
            tag:{
                type:String,
                default:'a'
            },
            replace:{
                type:Boolean,
                default:false
            }
        },
        render(h){
            let data={}
            //这里直接绑定点击事件
            data.on={
                    click:()=>{
                        if(this.replace){
                            this.$router.replace(this.to)
                        }else{
                            this.$router.push(this.to)
                        }
                    }
                }
            return h(this.tag,data,this.$slots.default)
        }
    })
    })
}
//最后直接导出即可
export default vueRouter
```