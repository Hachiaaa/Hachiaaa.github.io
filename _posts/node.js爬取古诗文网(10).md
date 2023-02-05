nodejs爬虫试水

## node.js爬虫
* node.js的爬虫主要用http库，或者https库来模拟客户端进行网页抓取
* 待抓取网站`https://so.gushiwen.org`，由于该网站只允许抓取前10页内容，所以先抓取作者页的前十页，之后抓取每个作者的前十页诗词(每页十首)，总共大概一万条，并保存到文本文档。
* 用到的库有，`fs` | `https` | `cheerio`，cheerio是node端的jquery.

下面直接上代码

首先根据作者页的页码抓取单个作者的诗词页面：   
```javascript
async function getAuthor(page){
        let fuckingauthorlist=[]
        fuckingauthorlist=await new Promise((resolve,reject)=>{
            https.get(`https://so.gushiwen.org/authors/default.aspx?p=${page}&c=`,(res)=>{
                let result=""
                res.on('data',(chunk)=>{
                    result+=chunk
                })
                res.on('end',()=>{
                    const $ =cheerio.load(result)
                    let authorlist=$('body > div.main3 > div.left > div.sonspic > div.cont > p:nth-child(3) > a')
                    let anchorlist=[]
                    for(let a in authorlist){
                        let anchor=authorlist[a].attribs
                        if((anchor!==undefined)&&(Object.keys(anchor).length!==0))anchorlist.push(anchor)
                    }
                    fuckingauthorlist=anchorlist.map((item,index)=>{
                        return item.href
                    })
                    resolve(fuckingauthorlist)
                })
            })
        })
        return fuckingauthorlist
       
}
````

然后获取前十页作者的诗词页面，这里要将循环放在异步容器里面，直接定义一个async 函数
```javascript
async function getAllAuthor(){
    let authorlist=[]
    for(let i=0;i<10;i++){
        let a=await getAuthor(i+1)
        authorlist.push(...a)
    }
    authorlist=authorlist.map((item)=>{
        return 'https://so.gushiwen.org'+item
    })
    return authorlist 
}
```

然后我们拿到了所有作者诗歌页面的首个链接，先定义抓取第一页的数据
```javascript
async function writeContent(url){  
    await new Promise((resolve,reject)=>{
    https.get(url,(res)=>{
        let result=''
        res.on("data",(chunk)=>{
            result+=chunk
        })
        res.on("end",()=>{
            const $=cheerio.load(result)
            let titlelist=[]
            let contentlist=[]
            let authorlist=[]
            let titlenodelist=$("body > div.main3 > div.left > div.sons > div.cont > p:nth-child(2) > a > b")
            let contentnodelist=$('body > div.main3 > div.left > div.sons > div.cont > div.contson')
            let authornodelist=$("body > div.main3 > div.left > div.sons > div.cont > p.source")
            for(let i=0;i<titlenodelist.length;i++){
                titlelist.push(titlenodelist.eq(i).text())
                authorlist.push(authornodelist.eq(i).text())
                contentlist.push(contentnodelist.eq(i).text())
            }
            console.log(titlelist.length,authorlist.length,contentlist.length)
            for(let i=0;i<titlelist.length;i++){
                fs.writeFileSync('./article/'+authorlist[0]+'.txt',titlelist[i]+'\r',{flag:'a'})
                fs.writeFileSync('./article/'+authorlist[0]+'.txt',authorlist[i]+'\r',{flag:'a'})
                fs.writeFileSync('./article/'+authorlist[0]+'.txt',contentlist[i]+'\r\r',{flag:'a'})
            }
            resolve()
        })
    })
    })
}
```

这一步完成之后，我们观察到作者诗歌页的页码可以通过构造url获得，首页的url为`https://so.gushiwen.org/authors/authorvsw_b90660e3e492A1.aspx`,其中的`A1`表示第一页，后面直接手动替换即可翻页。下面直接写入所有
```javascript
function writeAll(){
    getAllAuthor().then(res=>{
        res.forEach(async (item,index)=>{
            for(let i=0;i<10;i++){
                await writeContent(item.replace('A1','A'+(i+1)))
            }
        })
    })
}
```
最后只需要执行一下`writeAll`函数，大功告成！