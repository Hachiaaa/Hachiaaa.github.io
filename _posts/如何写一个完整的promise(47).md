符合Promise A+规范的promise实现

> 首先promise的执行顺序是then收集依赖-->异步触发resolve-->resolve执行依赖  
> Promise本质是一个状态机，包含pending，resolved，rejected三种状态。且状态的变化只能从pending到resolved或者rejected，即状态是单向变化的  
> then的链式调用，返回一层promise处理，并判断then回调函数的返回类型  
> 值穿透处理，如果then的参数不是function，应该忽略，否则链式调用会中断
> 对于状态已经为resolved,rejected的情况，直接执行then方法的回调

```javascript
//首先定义三种状态
const PENDING='PENDING'
const RESOLVED='RESOLVED'
const REJECTED='REJECTED'

class MyPromise{
    constructor(excutor){
        this.resolvedQueen=[] //成功回调队列
        this.rejectedQueen=[] //失败回调队列
        this._value=undefined  //储存then回调的参数
        this.status=PENDING
        let _resolve=(val)=>{
            //注意这里为了处理resolve为同步的情况，对resolve的执行用宏任务模拟，保证then回调先被推入到回调队列中

            const run=()=>{
                if(this.status!==PENDING) return
                this.status=RESOLVED
                this._value=val
                while(this.resolvedQueen.length){
                    let cb=this.resolvedQueen.shift()
                    cb(val)
                }
            }
            setTimeout(run)         
        }
        let _reject=(val)=>{
            const run=()=>{
                if(this.status!==PENDING) return
                this.status=REJECTED
                this._value=val
                while(this.rejectedQueen.length){
                    let cb=this.rejectedQueen.shift()
                    cb(val)
                }
            }
            setTimeout(run)
        }     
        excutor(_resolve,_reject)
    }
    then(fulfilledFn,rejectedFn){
        //值穿透
        typeof fulfilledFn !== 'function' ? fulfilledFn = value => value : null
        typeof rejectedFn !== 'function' ? rejectedFn = reason => {
         throw new Error(reason instanceof Error? reason.message:reason);
        } : null
        return new MyPromise((resolve,reject)=>{
            const newFulfilldFn=(val)=>{
                try{
                    let x=fulfilledFn(val)
                    //作值的判断，如果是Promise直接套一层then,否则直接resolve抛出
                    x instanceof MyPromise?x.then(resolve,reject):resolve(x)
                }catch(err){
                    reject(err)
                }
            }
            
            const newRejectedFn=(val)=>{
                try{
                    let x=rejectedFn(val)
                    x instanceof MyPromise?x.then(resolve,reject):resolve(x)
                }catch(err){
                    reject(err)
                }
            }
            //处理状态为resolved和rejected的情况
            switch(this.status){
                case PENDING:
                    this.resolvedQueen.push(newFulfilldFn)
                    this.rejectedQueen.push(newRejectedFn)
                    break
                case RESOLVED:
                    fulfilledFn(this._value)
                    break
                case REJECTED:
                    rejectedFn(this._value)
                    break
            }
        })
    }
    //catch就是then的语法糖
    catch(rejectFn){
        return this.then(undefined,rejectFn)
    }

    static resolve(value){
        // 根据规范, 如果参数是Promise实例, 直接return这个实例
        if(value instanceof MyPromise) return value 
        return new MyPromise(resolve => resolve(value))
    }
    static reject(reason){
        return new MyPromise((resolve,reject)=>reject(reason))
    }

    static all(promiseArr){
        let result=[]
        let index=0
        return new MyPromise((resolve,reject)=>{
            promiseArr.forEach((promise,i)=>{
                //resolve处理传入值不为promise的情况
                MyPromise.resolve(promise).then(
                    val=>{
                    index++
                    result[i]=val
                    if(index===promiseArr.length) resolve(result)    
                },
                err=>reject(err))
            })
        })
    }

    static race(promiseArr){
        return new MyPromise((resolve,reject)=>{
            promiseArr.forEach((promise,i)=>{
                //resolve处理传入值不为Promise的情况
                MyPromise.resolve(promise).then(
                    val=>resolve(val),
                    err=>reject(err)
                    )
            })
        })
    }
}

//测试
const p1 = new MyPromise((resolve, reject) => {
    resolve(1)          //同步executor测试
  })
  
p1.then(res => {
      console.log(res)
      return 2          //链式调用测试
    })
    .then()             //值穿透测试
    .then(res => {
      console.log(res)
      return new MyPromise((resolve, reject) => {
        setTimeout(()=>resolve(3),500)     //返回Promise测试
      })
    })
    .then(res => {
      console.log(res)
      throw new Error('reject测试')   //reject测试
    })
    .then(null,err=>console.log(err))

```