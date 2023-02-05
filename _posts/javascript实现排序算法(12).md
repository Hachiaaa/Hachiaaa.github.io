常见排序算法的JavaScript版本
1.冒泡排序
> 原理：将两个数相比较，较大的数依次冒泡到数列顶端。  
> 时间复杂度O(n^2),空间复杂度O(1）
```javascript
function bubble_sort(collection){
	let len=collection.length
	if(len<2){
		return collection
	}
	let list=JSON.parse(JSON.stringify(collection))  //写成纯函数的形式，避免改变入参
	for(let i=0;i<len;i++){
		for(let j=0;j<len-i-1;j++){
			if(list[j]>list[j+1]){
				let tmp=list[j+1]
				list[j+1]=list[j]
				list[j]=tmp
			}
		}
	}
	return list
}
```
2.选择排序
> 原理：在长度为n的序列中，第一次遍历n-1个数，将最小的数与第一个数交换；第二次遍历n-2个数，将最小的数与第二个数交换...  
> 时间复杂度O(n^2) ，空间复杂度O(1)  
```javascript
function select_sort(collection){
	let len=collection.length
	if(len<2){
		return collection
	}
	let list=JSON.parse(JSON.stringify(collection))
	for(let i=0;i<len;i++){
		for(let j=i+1;j<len;j++){
			if(list[j]<list[i]){
				let tmp=list[j]
				list[j]=list[i]
				list[i]=tmp
			}
		}
	}
	return list
}
```
3.插入排序
> 原理：在长度为n的序列中，假设前n项已经是有序序列，现在将第n个数插入到前n项中。跟玩扑克牌一样   
>  时间复杂度O(n^2)，空间复杂度O(1)  
 ```javascript
 function insert_sort(collection){
	let len=collection.length
	if(len<2){
	return collection
	} 
	let list=JSON.parse(JSON.stringify(collection))
	for(let i=0;i<len-1;i++){
		for(let j=i+1;j>0;j--){
			if(list[j]<list[j-1]){
				let tmp=list[j]
				list[j]=list[j-1]
				list[j-1]=tmp
			}
		}
	}
	return list
 }
 ```

4.快速排序
> 原理：先从序列中取出一个数作为key，将比这个数小的数放在左边，比这个数大的数放在右边。依次递归直至数组长度为1。  
> 平均时间复杂度:O(nlog2n)，空间复杂度O(log2(n+1))。快速排序需要一个栈空间来实现递归。
```javascript
function quick_sort(collection){
	const sort=(list,left=0,right=list.length-1)=>{
		if(left>=right) return
		let i=left;
		let j=right
		let baseval=list[i]
		while(i<j){
			while(j>i&&list[j]>baseval){
				 j--
			}
			while(i<j&&list[i]<=baseval){
				i++
			}
			//交换两个哨兵的位置
			if(i<j){
				let tmp=list[i]
				list[i]=list[j]
				list[j]=tmp
			}
		}
		//经过循环之后两个哨兵的位置重复了，交换哨兵位置和baseval
		list[left]=list[i]
		list[i]=baseval
		sort(list,left,i-1)
		sort(list,i+1,right)
	}
	let list=collection.concat()
	sort(list,0,list.length-1)
	return list
}
```

5.计数排序
> 原理:在一个以序列中出现的数为下标的数组中对序列中的数进行计数，之后依次取出来。
> 时间复杂度O(n),空间复杂度O(1)

```javascript
function count_sort(arr){
    if(arr.length<2) return list
    let res=[]
    let min=Math.min(...arr)
    let max=Math.max(...arr)
    let savelist=(new Array(max-min+1)).fill(0)
    for(let i=0;i<arr.length;i++){
        savelist[arr[i]-min]+=1
    }
    for(let j=0;j<savelist.length;j++){
        while(savelist[j]>0){
            res.push(j+min)
            savelist[j]--
        }
    }
    return res
}
```

6.撞库排序
> 随机洗牌算法

```javascript
function shuffle(arr){
	for(let i=arr.length;i>0;i--){
        let j=Math.floor(Math.random()*i)
        let tmp=arr[i-1]
        arr[i-1]=arr[j]
        arr[j]=tmp
	}
	return arr
}
```

> 撞库实现

```javascript
function random_sort(arr){
	const is_sorted=(list)=>{
		for(let i=0;i<list.length-1;i++){
			if(list[i]>list[i+1]) return false
		}
		return true
	}
	while(true){
		let list=shuffle(arr)
		if(is_sorted(list)){
			return list
		}
	}
}
```

7.基数排序
> 原理:将序列中的数先按个位排序，再按十位排序，以此类推
> 时间复杂度O(M*N),空间复杂度O(M)

```JavaScript
// 排序非负数
function radix_sort(array){
    let arr=array.slice()
    if(arr.length<0){
        return arr
    }
    let radix=10
    let res=[]
    for(let i=0;i<radix;i++){
        res.push([])
    }
    let max=String(Math.max(...arr)).length
    for(let index=0;index<max;index++){
        for(let i=0;i<arr.length;i++){
            let digital=Math.floor(arr[i]/(radix**index))%10
            res[digital].push(arr[i])
        }
        arr=[]
        for(let j=0;j<res.length;j++){
            while(res[j].length){
                arr.push(res[j].shift())
            }
        }
    }
    return arr
}
```

8.归并排序
> 原理:将待排序序列划分,分别排序再拼接

```javascript
function merge_sort(arr){
    if(arr.length<2) return arr
    var res=[]
    let middle=Math.floor(arr.length/2)
    let leftlist=merge_sort(arr.slice(0,middle))
    let rightlist=merge_sort(arr.slice(middle))
    while(leftlist.length&&rightlist.length){
        if(leftlist[0]<=rightlist[0]){
            res.push(leftlist.shift())
        }else{
            res.push(rightlist.shift())
        }
    }
    while(leftlist.length) res.push(leftlist.shift())
    while(rightlist.length) res.push(rightlist.shift())
    return res
}
```

9.希尔排序
> 原理:缩小增量排序，是一种更高效的插入排序，但是不稳定

```javascript
function shell_sort(array){
    let arr=array.slice()
    if(arr.length<2) return arr
    let index=Math.floor(arr.length/2)
    for(index;index>0;index=Math.floor(index/2)){
        for(let i=0;i<index;i++){
            for(let j=i;j<arr.length-index;j+=index){
                for(let k=j+index;k>0;k-=index){
                    if(arr[k]<arr[k-index]){
                        let tmp=arr[k]
                        arr[k]=arr[k-index]
                        arr[k-index]=tmp
                    }
                }
            }
        }
    }
    return arr
}
```
10.堆排序,构建大顶堆。大顶堆的特性是每个父节点都比子节点大

```javascript
function swap(arr,i,j){
    let tmp=arr[i]
    arr[i]=arr[j]
    arr[j]=tmp
}

//节点下沉
function downshift(arr,i,length){
    let tmp=arr[i]
    for(let j=i*2+1;j<length;j=j*2+1){
        tmp=arr[i]
        if(j+1<length&&arr[j+1]>arr[j]){
            j++
        }
        if(tmp<arr[j]){
            swap(arr,i,j)
            i=j
        }else{
            break
        }
    }
}

function heapsort(arr){
    let list=arr.slice()
    let index=Math.floor(list.length/2-1)
    for(let i=index;i>=0;i--){
        downshift(list,i,list.length)
    }
    for(let i=list.length-1;i>0;i--){
        swap(list,i,0)
        downshift(list,0,i)
    }
    return list
}
```