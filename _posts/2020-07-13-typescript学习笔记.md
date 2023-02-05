---
layout: post
title: "typescript学习笔记"
date: 2020-07-13
sitemap: false
keywords: "blog"
description: "🚀"
---

TS笔记

## ts安装，编译
```javascript
npm install typescript -g
tsc xxx.ts
```
typescript在编译时对类型进行静态检查，如果发现错误，在编译时报错

## ts原始数据类型(primitive data types)  
string,number,boolean,undefined,null,symbol  

在ts中，可以用void表示没有任何返回值的函数,声明为void类型的变量可以赋值为undefined和null。实际上null和undefined是所有类型的子类型。
```javascript
function alertName():void{
    alert("i am so shy")
}
```

## 任意值any
变量如果在声明的时候未指定类型，那么它会被识别为任意类型，任意类型可以随意赋值，访问属性和方法

## 类型推论（type inference)
typescript会在没有明确指定类型的时候推测出一个类型。所以以下代码会报错
```javascript
let stringtype='string'
stringtype=1
```

## 联合类型(union types)
1.联合类型使用`|`分割每个类型  
2.当ts不确定一个联合类型的变量到底是哪个类型的时候，会访问所有类型共有的属性和方法，所以以下代码报错
```javascript
function getLength(something:string | number):number{
    return something.length  //number类型没有length属性
}
```
3.联合类型的变量在被赋值的时候，会根据类型推论的规则推断出一个类型

## 对象的类型-接口(interfaces)
1.ts中的接口除了可以对类的一部分行为进行抽象以外，也常用于对对象的形状(shape)进行描述。定义的变量不能比接口少也不能比接口多，形状必须保持一致。如果希望不要完全匹配一个形状，那么可以用可选属性  
2.有时候如果希望一个接口有任意的属性，可以用任意类型。值得注意的是，一旦定义了任意属性， 那么确定属性和可选属性的类型都必须是它类型的子集。  
3.只读属性
```javascript
interface Person{
    name:string,
    age:number,
    readonly id:number,
    salary?:number,  //可选属性
    [propName:string]:any
}
let tom:Person={
    name:'Tom',
    age:15,
    id:1
}
```

## 数组类型
1.【类型+方括号】表示  
2.数组泛型(array generic)  
```javascript
let fib:Array<number>=[1,1,2,3,5]
```

## 函数类型
1.函数声明  
2.函数表达式(在ts中`=>`用来表示函数的定义，左边是输入类型，右边是输出类型),也可以用接口定义函数形状 
3.函数定义可以用可选参数，必须放在必选参数后面  
4.参数可以有默认值，ts会将添加了默认值的参数识别为可选参数  
5.可以有rest参数，实际上rest是一个数组，所以可以用数组的类型来定义
```javascript
//完整函数表达式定义
let mySum:(x:number,y:number)=>number=function(x:number,y:number):number{
    return x+y
}
//接口定义函数
interface AddFn{
    (x:number,y:number):number
}
let mySum:AddFn=function(x:number,y:number):number{
    return 
}
```
6.函数的重载，ts会优先从最前面的函数定义开始匹配，所以多个函数定义如果有包含关系，需要优先把精确的定义写在前面。
```javascript
function reverse(x:number):number
function reverse(x:string):string
function reverse(x:number|string):number|string{
    if(typeof x==='number'){
        return Number(x.toString().split("").reverse().join(""))
    }else if(typeof x==='string'){
        return x.split("").reverse().join("")
    }
}
```

## 类型断言(type assertion)
1.将一个联合类型断言为其中一个类型。当ts不确定一个联合类型的变量到底是哪个类型的时候，我们只能访问此联合类型中共有的属性或方法。
```javascript
interface Cat{
    name:string,
    run():void
}
interface Fish{
    name:string,
    swim():void
}
function getName(animal:Cat|Fish){
    if(typeof (animal as Fish).swim==='function'){
        return true
    }
    return false
}
```
2.将一个父类断言为更加具体的子类，可以是js中的类，或者是ts的接口
3.将任何一个类型断言为any  
4.将any类型断言为一个具体的类型  

