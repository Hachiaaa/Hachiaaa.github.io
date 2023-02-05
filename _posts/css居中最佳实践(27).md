css居中的常见方法

## 水平居中
* 行内元素，`text-align：center`  
* 宽度确定元素
> * `margin:0 auto`
> * `绝对定位组合负的margin值`  

* 宽度位置元素
> * flex布局，`justify-content`  
> * 绝对定位+transform  
> * 父元素设置为`text-align：center`，子元素设置为`display:inline-block`  

## 垂直居中
* `line-height`纯文本元素  
* 绝对定位+`margin:auto`，`top,bottom,left,right`都设置为0  
* 绝对定位+transform
* flex布局下，`margin:auto`
* table布局，父级转换为表格，子级设置为table-cell，然后利用`vertical-align:middle`

```html
<div class='parent'>
    <div class='son'>
        <div class='content'>
        </div>
    </div>
</div>
```

```css
.parent{
    display:table;
    width:200px;
    height:200px;
    border:1px solid red;
}
.son{
    display:table-cell;
    vertical-align:middle;
}
.content{
    width:50px;
    height:50px;
    border:1px dashed green;
}
```