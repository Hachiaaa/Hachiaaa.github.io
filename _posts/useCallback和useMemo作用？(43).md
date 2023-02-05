pureComponent,React.memo(),useMemo(),useCallback()

1.pureComponent会重写shouldComponentUpdate生命周期函数,进行前后props和state的浅比较
下面是一个pureComponent不会更新的例子

```javascript
import React,{useCallback,useState,useMemo,Component,PureComponent} from 'react';

class App extends Component{
  constructor(props){
    super(props)
    this.state={
      count:1,
      childData:['luer']
    }
    this.clickCallback=this.clickCallback.bind(this)
  }

  clickCallback(){
    this.state.childData.push('haha')
    this.setState({
      ...this.state,
      count:this.state.count+1
    })
  }
  render(){
    console.log(this.state.childData)
    return(
      <>
        <button onClick={this.clickCallback}>add</button>
        <div>{this.state.count}</div>
        <Child data={this.state.childData}/> //这里传递了一个数组引用对象,PureComponent不会更新
      </>
    )
  }
}

class Child extends PureComponent{
  constructor(props){
    super(props)
  }
  render(){
    console.log('render') //不会打印render
    return(
    <h1>{this.props.data.map((item,index)=>{
      return(
        <div key={index}>{item}</div>
      )
    })}</h1>
    )
  }
}

export default App

```

2.React.memo(),和pureComponent类似,可以用于包裹函数组件
如果不用memo包裹,则父组件中的计数器更新,虽然子组件前后props没有变化,但是由于子组件包裹在父组件内,子组件仍然会不必要的渲染,造成性能浪费

```javascript
class App extends Component{
  constructor(props){
    super(props)
    this.state={
      count:1,
      mes:'fuck'
    }
    this.clickCallback=this.clickCallback.bind(this)
  }

  clickCallback(){
    this.setState({
      count:this.state.count+1
    })
  }
  render(){
    return(
      <>
        <button onClick={this.clickCallback}>add</button>
        <div>{this.state.count}</div>
        <Child message={this.state.mes}/>
      </>
    )
  }
}

function Child(props){
  console.log('child render')
  return(
    <h1>{props.message}</h1>
  )
}

Child=React.memo(Child) //重要
export default App
```

3.useCallback()
包裹一个函数,组件刷新时返回旧函数的引用。

```javascript
function App(){
  const [count,setCount]=useState(0)
  const [name,setName]=useState('luer')
  const changeName=useCallback((newname)=>setName(newname),[])
  //const changeName=newname=>setName(newname)
  //由于changeName是一个函数,每次App刷新的时候回重新创建一个changeName,函数是引用对象,所以即使被memo包裹,子组件仍然会刷新。
  //这是就要用useCallback包裹这个函数,返回一个旧的函数引用,数组依赖项表示只有依赖项发生变化时才返回一个新的函数引用
  return(
    <>
      <button onClick={()=>setCount(c=>c+1)}>add</button>
      <h1>parent count{count}</h1>
      <Child onClick={changeName} name={name}/>
    </>
  )
}

function Child(props){
  console.log('child render')
  return(
    <button onClick={()=>props.onClick('fuck')}>{props.name}</button>
  )
}
Child=React.memo(Child)
export default App
```

4.useMemo()
和useCallback一样,当传递一个引用对象的props时,可以用useMemo包裹,提升性能

```javascript
function App(){
  const [count,setCount]=useState(0)
  const [name,setName]=useState('luer')
  //const info={name} 这种用法会导致重新渲染子组件
  const info=useMemo(()=>({name}),[name]) //用useMemo包裹一层,返回函数执行返回的对象的引用
  const changeName=useCallback((newname)=>setName(newname),[])
  return(
    <>
      <button onClick={()=>setCount(c=>c+1)}>add</button>
      <h1>parent count{count}</h1>
      <Child onClick={changeName} info={info}/>
    </>
  )
}

function Child(props){
  console.log('child render')
  return(
    <button onClick={()=>props.onClick('fuck')}>{props.info.name}</button>
  )
}
Child=React.memo(Child)
export default App
```
