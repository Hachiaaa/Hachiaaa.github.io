redux核心原理解析

```javascript
//createStore函数接受reducer，初始状态，中间件
function createStore(reducer,initState,rewriteCreateStore){
    if(typeof initState==='function'){
        rewriteCreateStore=initState
        initState=undefined
    }
	if(rewriteCreateStore){
		let newCreateStore=rewriteCreateStore(createStore)
		return newCreateStore(reducer,initState)
	}
	let state=initState 
	let listeners=[]
	function subscribe(fn){
		listeners.push(fn)
	}
	function getState(){
		return state
	}
	function dispatch(action){
		state=reducer(state,action)
		listeners.forEach(item=>{
			item()
		})
	}
	dispatch({type:Symbol()})
	return {
		getState,
		subscribe,
		dispatch
	}
}

let countInitState={
	count:1
}

function countReducer(state,action){
	if(!state){
		state=countInitState
	}
	switch(action.type){
		case 'increment':
			return{
				...state,
				count:state.count+action.num
			}
		case 'decreament':
			return{
				...state,
				count:state.count-action.num
			}
		default:
			return state
	}
}

let infoInitState={
	name:'cheong kwan jang',
	desc:'korean red ginseng'
}

function infoReducer(state,action){
	if(!state){
		state=infoInitState
	}
	switch(action.type){
		case 'setName':
			return{
				...state,
				name:action.name
			}
		case 'setDesc':
			return{
				...state,
				desc:action.desc
			}
		default:
			return state
	}
}

function combineReducers(reducers){
	let reducerkey=Object.keys(reducers)
	return function(state={},action){
		const nextState={}
		reducerkey.forEach((item,index)=>{
			let singleState=reducers[item](state[item],action)
			nextState[item]=singleState
		})
		return nextState
	}
}


let state={
	counter:{
		count:1
	},
	info:{
		name:'cheongkwanjang',
		desc:'korean red ginseng'
	}
}

let reducer=combineReducers({
	counter:countReducer,
	info:infoReducer
})

//日志记录中间件
let loggerMiddleware=(store)=>(next)=>(action)=>{
	console.log('oldstate',store.getState())
	console.log('action',action.type)
	next(action)
	console.log('newstate',store.getState())
}

//时间中间件
let timeMiddleware=(store)=>(next)=>(action)=>{
	console.log('time right now',new Date().toLocaleString())
	next(action)
}

//thunk中间件，允许我们dispatch一个函数，在函数中间可以同时发起多个action

let thunkMiddleware=(store)=>(next)=>(action)=>{
    if(typeof action ==='function'){
        let {dispatch}=store
        action(dispatch)
    }
    next(action)
}


function applyMiddlewares(...middlewares){
	function compose(...fns){
		if(fns.length===1){
			return fns[0]
		}else{
			return fns.reduce((a,b)=>((...args)=>a(b(...args))))
		}

	}
	return function(oldcreatestore){
		return function newCreateStore(reducer,initState){
			let store=oldcreatestore(reducer,initState)
			let chain=middlewares.map(item=>item(store))
			store.dispatch=compose(...chain)(store.dispatch)
			return store
		}
	}
}

let newCreateStore=applyMiddlewares(timeMiddleware,loggerMiddleware)

let store=createStore(reducer,newCreateStore)

store.subscribe(()=>{
	console.log(store.getState())
})

store.dispatch({
	type:'setName',
	name:'haha'
})
```