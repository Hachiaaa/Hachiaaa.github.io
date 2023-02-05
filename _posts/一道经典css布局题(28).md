css深入理解

> 问题描述: 实现一个div垂直居中, 其距离屏幕左右两边各10px, 其高度始终是宽度的50%。同时div中有一个文字A，文字需要水平垂直居中。

## 利用height:0;padding-bottom:50%;

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        *{
            margin: 0;
            padding: 0;
        }
        html,body{
            width:100%;
            height:100%;
        }
        .outter{
            display:flex;
            align-items: center;
            margin:0 10px;
            height:100%;
        }
        .wrapper{
            width:100%;
            background:red;
            height:0;
            padding-bottom:50%;
            position:relative;
        }
        .box{
            width:100%;
            height:100%;
            position: absolute;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size:50px;
        }
    </style>
</head>
<body>
    <div class='outter'>
        <div class='wrapper'>
            <div class='box'>A</div>
        </div>
    </div>
</body>
</html>
```


* 注意点一：`padding-bottom`百分比值是相对于父元素的宽度值  
* 注意点二：父元素相对定位，子元素绝对定位。子元素的宽高百分比是针对父元素的(padding+content)的，不包括border；如果子元素不是绝对定位，那么宽高百分比在标准盒模型下相对于content，在IE盒模型下相对于padding+content+border

## 利用calc和vw

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        html,body{
            width:100%;
            height:100%;
        }
        .wrapper{
            position: relative;
            width: 100%;
            height:100%
        }
        .box{
            width:calc(100vw - 20px);
            height: calc(50vw - 10px);
            background-color: red;
            margin-left: 10px;
            margin-right:10px;
            position: absolute;
            top:50%;
            transform: translateY(-50%);
            display: flex;
            align-items:center;
            justify-content: center;
            font-size:100px;
        }
    </style>
</head>
<body>
    <div class='wrapper'>
        <div class='box'>A</div>
    </div>
</body>
</html>
```