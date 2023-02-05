React生命周期（包括hooks）

React v16.0前的生命周期  
1.组件初始化阶段（initialization）
调用constructor，注入父组件的props，初始化state

2.组件挂载阶段（mounting）
> 此阶段分为componentWillMount,render,componentDidMount

* componentWillMount,组件挂载到DOM前调用，且只会被调用一次，可以提前到constructor，很少使用

* render，根据组件的props和state，return一个React元素

* componentDidMount，组件挂载到DOM后调用，且只会被调用一次

3.组件更新阶段（update）
> react组件更新机制，setState引起的state更新或者父组件重新render引起的props更新，更新后的state和props相对之前无论是否有变化，都将引起子组件的重新render。

> 此阶段分为componentWillReceiveProps,shouldComponentUpdate,componentWillUpdate,render,componentDidUpdate

* componentWillReceiveProps(nextProps),可以对比前后props是否发生改变，如果发生改变执行的东西，比如setState

* shouldComponentUpdate(nextProps,nextState),该方法通过前后props，state的比较，返回true将继续执行更新过程，返回false则当前组件更新停止。以此来减少不必要的渲染
> 这边也可以看出，就算componentWillReceiveProps()中执行了this.setState，更新了state，但在render前（如shouldComponentUpdate，componentWillUpdate），this.state依然指向更新前的state，不然nextState及当前组件的this.state的对比就一直是true了。

* componentWillUpdate(nextProps,nextState),在render调用前执行，这一步不能更改state，否则会造成死循环。

* componentDidUpdate(prevProps,prevState),此方法在更新后调用，可以操作组件更新的DOM。 两个参数分别指向更新前的props和state

4.组件卸载阶段
>该阶段只有一个生命周期，componentWillUnmount

* componentWillUnmount,在组件卸载前被调用，可以在这里执行一些清理工作

React hooks与之前对比

* constructor==useState
* getDerivedStateFromProps ==useState里的update函数
* shouldComponentUpdate==useMemo
* render==函数本身 
* componentDidMount==useEffect
* componentDidUpdate==useEffect
* componentWillUnmount==useEffect里面返回的函数