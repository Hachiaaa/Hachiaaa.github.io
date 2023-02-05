详解React中的setState

根据Dan神的回答整理而成  
1.保证内部的一致性
> 首先，props本身是异步更新的，因为只有父组件重新渲染（re-render)了才能知道新的props。React为了在内部保证一致性和状态提升的安全性，所以this.state也是异步更新的。

我们先假设state同步更新，下面的代码可以预期工作
```javascript
console.log(this.state.value) // 0
this.setState({ value: this.state.value + 1 });
console.log(this.state.value) // 1
this.setState({ value: this.state.value + 1 });
console.log(this.state.value) // 2
```
然而，我们常常需要将状态提升到父组件，以供多个兄弟组件共享
```javascript
-this.setState({ value: this.state.value + 1 });
+this.props.onIncrement(); // 在父组件中做同样的事
```
然而下面的代码不会预期工作
```javascript
console.log(this.props.value) // 0
this.props.onIncrement();
console.log(this.props.value) // 0
this.props.onIncrement();
console.log(this.props.value) // 0
```
这是因为this.props并不会立即更新，在没有重新渲染父组件之前，我们不能立即更新props

2.性能优化  
React会根据不同的调用源，给不同的setState()调用分配不同的优先级，调用源包括事件处理，网络请求，动画等。如果给某些更新分配低优先级，那么就可以把他们的渲染分拆为几个毫秒的块，用户也不会注意到。
