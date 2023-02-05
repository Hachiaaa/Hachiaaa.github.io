---
layout: post
title: "React的useEffect"
date: 2020-07-28
sitemap: false
keywords: "blog"
description: "🚀"
---

useEffect解决依赖数组频繁变化

来看一个定时器的例子
```javascript
import React from 'react';

function App(){
  let [count,setCount]=React.useState(0)
  React.useEffect(()=>{
    const id=setInterval(() => {
      setCount(count+1)//解决办法2：使用setCount的函数形式 setCount(c=>c+1),此时下方不用依赖项
    }, 1000);
    return ()=>{
      console.log('unmount')
      clearInterval(id)
    }
  },[])//不设置依赖,则useEffect内部定时器拿到的count永远是0,一直在循环执行0+1的操作
  //解决办法1：设置依赖[count],此时的问题是定时器会一直清除然后重建(因为Effect内部的代码会因为count的改变一直执行)
  return (
    <h1>{count}</h1>
  )
}

export default App

```