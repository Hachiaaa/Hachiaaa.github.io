css属性

## 背景background
* background-color
* background-image url("link")
* background-repeat no-repeat, repeat-x, repeat-y
* background-attachment fixed(相对视口固定), scroll,local(对于可滚动的元素，即overflow:scroll的元素，该属性会随内容的滚动而滚动)
* background-position 背景图像的定位
* background-size 指定背景图像的大小
* background-origin  指定背景图像的位置区域content-box, padding-box, border-box
* background-clip  指定背景裁切从哪里开始  content-box，padding-box，border-box

# 文本text
* color 文字颜色
* text-align 文字对齐方式 justify，center，left，right
* text-decoration   overline，line-through，underline，none
* text-transform 文本转化 uppercase，lowercase，capitalize
* text-indent 文本缩进
* vertical-aligh
* line-height 设置行高
* text-shadow 文本阴影，四个值分别是 h-shadow,v-shadow,blur,color
* box-shadow 水平阴影，垂直阴影，模糊距离，阴影扩展半径，颜色，是否为内阴影(inset)
* text-overflow 当文本在元素内溢出时如何处理   ellipsis ，clip
* word-wrap  是否允许长文本换行 break-word，normal
* word-break  规定换行规则 break-all  keep-all

# 字体font
* font-family 指定文本的字体系列
* font-size
* font-style  normal，italic，oblique
* font-weight  指定字体的粗细
* 字体扩展 @font-face

# 链接link
**链接直接设置文本以及背景修饰即可**
> * a:link  
> * a:visited  
> * a:hover  
> * a:active    

# 列表ul
* list-style-type  circle，square，upper-roman，lower-alpha
* list-style-image  将图像作为列表标记项
* list-style-position inside，outside 规定列表项标记的位置

# 表格table
* border-collapse  collapse 设置表格的边框是否被折叠成单一的边框

# 边框border
* border-width
* border-style(required)
* border-color
* border-radius 四个值，左上-右上-右下-左下   三个值写法右上左下相同      两个值写法左上右下相同，右上左下相同
* border-image 使用图片建立边框